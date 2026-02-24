---
layout: post
title: "Bare-Metal UART on STM32F4 Without the HAL"
date: 2026-02-24
category: projects
---

Interrupt-driven UART driver for the STM32F401xE Nucleo in C++17. No HAL, no RTOS, no heap.

![UART-Cpp system overview](/assets/images/projects/uart-cpp-system.svg)

CubeMX and the ST HAL work. They also generate 20,000 lines of code before you write a single line of your own. Replacing all of it with direct register access takes about 200 lines. Every decision is visible, every register write has a reason. The full source is on [GitHub](https://github.com/HueCodes/UART-cpp).

## Clocks

The Nucleo-F401RE provides an 8 MHz external oscillator (HSE). The target is 84 MHz SYSCLK, which requires the main PLL. M divides the HSE down to 1 MHz for the VCO input, the reference manual recommends keeping this between 1-2 MHz for low jitter. N multiplies the VCO input to 336 MHz. P divides that down to 84 MHz. Q is set to 7 to keep the 48 MHz USB clock in spec, even though this project doesn't use USB.

```cpp
RCC->PLLCFGR = (8u   << RCC_PLLCFGR_PLLM_Pos)   // /8  -> VCO_in  = 1 MHz
             | (336u << RCC_PLLCFGR_PLLN_Pos)    // x336 -> VCO_out = 336 MHz
             | (1u   << RCC_PLLCFGR_PLLP_Pos)    // /4  -> SYSCLK  = 84 MHz (bits=01)
             | RCC_PLLCFGR_PLLSRC_HSE
             | (7u   << RCC_PLLCFGR_PLLQ_Pos);   // /7  -> USB     = 48 MHz
```

Flash latency has to be configured before switching the clock source. At 3.3 V and 84 MHz the F401 needs 2 wait states. Switch first and the CPU reads corrupted instructions from flash before the latency is set. The correct order is: set latency, enable PLL, wait for lock, then switch SW to PLL and confirm via SWS. APB1 runs at SYSCLK/2 because its maximum is 45 MHz. APB2 runs at SYSCLK/1. USART2 sits on APB1 at 42 MHz, which feeds directly into the baud rate calculation.

![Clock tree: HSE to USART2](/assets/images/projects/uart-cpp-clock-tree.svg)

## GPIO and USART2 Init

The Nucleo hard-wires USART2 to the ST-Link, so the pin mapping is not configurable: PA2 is TX and PA3 is RX, both on alternate function 7. Before touching any GPIO register, the AHB1 and APB1 clocks for GPIOA and USART2 must be enabled. There is a well-known errata behavior on STM32F4 where reading immediately after enabling a peripheral clock can return stale data, so the driver does a dummy read of APB1ENR to flush the write buffer before proceeding.

Both PA2 and PA3 are set to alternate function mode (MODER = 0b10), push-pull output type, high-speed slew rate, no pull resistors, and AF7 in the low alternate function register. USART2 is disabled (UE cleared) before writing the configuration registers. Data width is fixed at 8 bits (M=0). Parity and stop bits are configurable at construction time via enum class parameters, which keeps the public API clean without magic numbers.

The baud rate register is derived at runtime from the actual APB1 frequency rather than a hardcoded value. This matters if the prescaler changes: the driver reads CFGR, extracts the PPRE1 field, decodes the divider, and computes PCLK1 from SystemCoreClock. The rounding correction `+ baud/2` keeps the result within one LSB of the ideal.

```cpp
uint32_t ppre1 = (RCC->CFGR >> RCC_CFGR_PPRE1_Pos) & 0x7u;
uint32_t div   = (ppre1 < 4u) ? 1u : (1u << (ppre1 - 3u));
uint32_t pclk1 = SystemCoreClock / div;
USART2->BRR    = (pclk1 + baud_ / 2u) / baud_;
```

At 42 MHz and 115200 baud this produces BRR = 364, giving a 0.016% error against the ideal divisor. RXNEIE is enabled before UE so the interrupt is armed the moment the peripheral starts receiving. TXEIE is left off intentionally and enabled only when there is data to send.

## Interrupt-Driven TX and RX

![Interrupt-driven TX/RX flow](/assets/images/projects/uart-cpp-interrupt-flow.svg)

Polling SR in a loop wastes the entire CPU while USART2 runs at its own pace. At 115200 baud each bit takes 8.7 µs and each byte takes roughly 87 µs. Interrupts give that time back. The driver uses USART2_IRQHandler at NVIC priority 5, which sits below the default SysTick priority and above any application-level IRQs.

RX is always interrupt-driven. RXNEIE fires whenever the receive data register is not empty. The ISR reads DR and pushes the byte into the RX ring buffer. The application calls `receive()` which spins on the buffer, or `rx_ready()` for a non-blocking poll. TX uses on-demand TXEIE. `send()` pushes a byte into the TX ring buffer, then sets TXEIE in CR1. The ISR fires when TDR is empty, pops a byte, and writes it to DR. When the buffer drains, the ISR clears TXEIE itself. This prevents the ISR from re-firing immediately after servicing when there is nothing left to transmit.

```cpp
void Uart::send(uint8_t byte)
{
    while (!tx_buf_.push(byte)) {}  // spin if buffer full
    USART2->CR1 |= USART_CR1_TXEIE;
}
```

```cpp
void Uart::irq_handler()
{
    get_and_clear_errors();

    uint32_t sr  = USART2->SR;
    uint32_t cr1 = USART2->CR1;

    if ((cr1 & USART_CR1_RXNEIE) && (sr & USART_SR_RXNE)) {
        uint8_t byte = static_cast<uint8_t>(USART2->DR & 0xFFu);
        rx_buf_.push(byte);
    }

    if ((cr1 & USART_CR1_TXEIE) && (sr & USART_SR_TXE)) {
        uint8_t byte;
        if (tx_buf_.pop(byte)) {
            USART2->DR = byte;
        } else {
            USART2->CR1 &= ~USART_CR1_TXEIE;
        }
    }
}
```

The ISR reads both CR1 and SR before acting. Checking the enable bit alongside the status bit avoids a race where a flag is set in hardware between the point where software clears the enable and the point where the ISR exits. The ISR is declared `extern "C"` because the vector table holds a C function pointer. Without it, C++ name mangling produces a symbol the linker cannot match to the vector slot, and the slot falls back to `Default_Handler`.

STM32F4 USART error flags (overrun, framing, noise) require a specific two-step clear: read SR, then read DR. The driver calls `get_and_clear_errors()` at the top of every ISR invocation. If any error flags are set, DR is consumed inside that function, which also clears RXNE. The errored byte is discarded and the rest of the ISR sees a clean SR. This matches the sequence mandated in the F401 reference manual.

## Ring Buffer and SPSC Lock-Freedom

Both buffers are a 256-byte power-of-two SPSC ring buffer templated over element type and capacity. The power-of-two constraint replaces modulo with a bitmask, which is relevant in an ISR where you want the shortest possible critical path.

```cpp
template <typename T, size_t N>
class RingBuffer {
    static_assert((N & (N - 1)) == 0, "N must be a power of 2");
    static constexpr size_t MASK = N - 1;

    volatile size_t head_{0};
    volatile size_t tail_{0};
    T buf_[N];
    ...
};
```

The SPSC property is what removes the need for atomics. For the TX buffer, the application thread is the sole producer (writes `head_`), and the ISR is the sole consumer (reads `head_`, writes `tail_`). For the RX buffer the roles reverse. Because there is exactly one writer and one reader for each index, no compare-exchange or critical section is needed. Only `head_` and `tail_` carry `volatile` because the compiler must not cache their values across reads. The buffer data itself is not volatile since the ordering guarantee comes through the index writes. Usable capacity is 255 bytes: one slot stays permanently unused to distinguish full from empty without a separate counter.

## Startup

There is no vendor startup file. `startup.cpp` contains the complete 98-entry vector table for the STM32F401xE (16 ARM Cortex-M4 system exceptions followed by 82 device IRQs) and `Reset_Handler`. Every slot that the application does not override gets `__attribute__((weak, alias("Default_Handler")))`, which spins in `for(;;){}`. An unhandled interrupt halts in a known location a debugger can attach to, rather than jumping through an undefined pointer.

```cpp
extern "C" void Reset_Handler()
{
    // copy .data from flash load address to RAM
    for (uint32_t *src = &_sidata, *dst = &_sdata; dst < &_edata;)
        *dst++ = *src++;

    // zero .bss
    for (uint32_t *dst = &_sbss; dst < &_ebss;)
        *dst++ = 0;

    __libc_init_array();   // C++ static constructors
    main();
    for (;;) {}
}
```

`__libc_init_array()` runs before `main()`, which matters because `s_uart` in uart.cpp is a static object whose constructor must fire to zero the ring buffer indices.

## Build

The toolchain file targets Cortex-M4F with hardware FPU: `-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard`. `-fno-exceptions -fno-rtti` removes the C++ runtime support code that would otherwise require heap and significantly bloat the binary. `-ffunction-sections -fdata-sections` combined with `-Wl,--gc-sections` lets the linker dead-strip anything not reachable from `Reset_Handler`. `-specs=nano.specs` links against newlib-nano instead of the full newlib, and `-specs=nosys.specs` provides stub syscalls. A custom `syscalls.cpp` overrides `_write` to forward `printf` output through the UART driver.

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=cmake/arm-none-eabi.cmake
cmake --build build
cmake --build build --target flash
```

The post-build steps produce an Intel HEX and a flat binary alongside the ELF. Flash with OpenOCD via the ST-Link on the Nucleo. Connect at 115200 8N1 on `/dev/tty.usbmodem*` (macOS) or `/dev/ttyACM0` (Linux). Every character echoes back, with `\r` expanded to `\r\n`.

---

[View on GitHub](https://github.com/HueCodes/UART-cpp)
