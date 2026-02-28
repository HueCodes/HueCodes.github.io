---
layout: post
title: "Bare-Metal UART on STM32F4 Without the HAL"
date: 2026-02-24
category: projects
---

Interrupt-driven UART driver for the STM32F401xE Nucleo in C++17. No HAL, no RTOS, no heap.

![UART-Cpp system overview](/assets/images/projects/uart-cpp-system.svg)

CubeMX and the ST HAL work. They also generate 20,000 lines before you write a single line of your own. Replacing the relevant portions with direct register access takes about 200 lines. Every decision is visible, every register write has a reason. Source on [GitHub](https://github.com/HueCodes/UART-cpp).

## Clocks

The Nucleo-F401RE has an 8 MHz HSE. Target is 84 MHz SYSCLK via the main PLL. M divides to 1 MHz VCO input (1-2 MHz recommended for low jitter), N multiplies to 336 MHz, P divides to 84 MHz. Q=7 keeps the 48 MHz USB clock in spec.

```cpp
RCC->PLLCFGR = (8u   << RCC_PLLCFGR_PLLM_Pos)   // /8  -> VCO_in  = 1 MHz
             | (336u << RCC_PLLCFGR_PLLN_Pos)    // x336 -> VCO_out = 336 MHz
             | (1u   << RCC_PLLCFGR_PLLP_Pos)    // /4  -> SYSCLK  = 84 MHz (bits=01)
             | RCC_PLLCFGR_PLLSRC_HSE
             | (7u   << RCC_PLLCFGR_PLLQ_Pos);   // /7  -> USB     = 48 MHz
```

Flash latency must be configured before switching the clock source. At 84 MHz the F401 needs 2 wait states. Switch first and the CPU reads corrupted instructions. Correct order: set wait states, enable PLL, wait for PLLRDY, switch SW, confirm SWS. APB1 runs at SYSCLK/2 (max 45 MHz). USART2 sits on APB1 at 42 MHz.

![Clock tree: HSE to USART2](/assets/images/projects/uart-cpp-clock-tree.svg)

## GPIO and USART2 Init

The Nucleo hard-wires USART2 to the ST-Link: PA2 is TX, PA3 is RX, both AF7. AHB1 and APB1 clocks for GPIOA and USART2 must be enabled before touching any GPIO register. A dummy read of APB1ENR flushes the write buffer — an F4 errata where reading immediately after a clock enable can return stale data.

PA2 and PA3 are set to alternate function mode (MODER=0b10), push-pull, high-speed, no pull resistors, AF7 in AFRL. USART2 is disabled (UE cleared) before writing config registers. Parity and stop bits are configurable at construction time via enum class parameters.

The baud rate register is computed at runtime from the actual APB1 frequency, not a hardcoded constant. The driver reads CFGR, extracts PPRE1, decodes the divider, and computes PCLK1. The `+baud/2` rounding keeps the result within one LSB of ideal.

```cpp
uint32_t ppre1 = (RCC->CFGR >> RCC_CFGR_PPRE1_Pos) & 0x7u;
uint32_t div   = (ppre1 < 4u) ? 1u : (1u << (ppre1 - 3u));
uint32_t pclk1 = SystemCoreClock / div;
USART2->BRR    = (pclk1 + baud_ / 2u) / baud_;
```

At 42 MHz and 115200 baud this produces BRR = 364, giving a 0.016% error against the ideal divisor. RXNEIE is enabled before UE so the interrupt is armed the moment the peripheral starts receiving. TXEIE is left off intentionally and enabled only when there is data to send.

## Interrupt-Driven TX and RX

![Interrupt-driven TX/RX flow](/assets/images/projects/uart-cpp-interrupt-flow.svg)

Polling SR in a loop wastes the CPU. At 115200 baud each byte takes ~87 µs. The driver uses USART2_IRQHandler at NVIC priority 5, below SysTick and above application IRQs.

RX is always interrupt-driven. RXNEIE fires on each received byte; the ISR pushes it into the RX ring buffer. TX is on-demand: `send()` pushes to the TX buffer and sets TXEIE. The ISR fires when TDR is empty, pops a byte, writes it to DR. When the buffer drains, the ISR clears TXEIE — preventing it from re-firing with nothing to send.

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

The ISR reads CR1 alongside SR before acting. Checking the enable bit with the status bit avoids a race where hardware sets a flag after software clears the enable but before the ISR exits. The ISR is `extern "C"` — C++ name mangling otherwise produces a symbol the linker cannot match to the vector slot, silently falling back to `Default_Handler`.

STM32F4 USART error flags (overrun, framing, noise) require a two-step clear: read SR, then read DR. The driver calls `get_and_clear_errors()` at the top of every ISR. Error flags set means DR is consumed inside that function, clearing RXNE. The errored byte is discarded and the rest of the ISR sees a clean SR, per the F401 reference manual.

## Ring Buffer and SPSC Lock-Freedom

Both buffers are 256-byte power-of-two SPSC ring buffers templated over element type and capacity. Power-of-two replaces modulo with a bitmask, relevant in an ISR where every cycle counts.

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

SPSC removes the need for atomics. The TX buffer has one producer (application thread, writes `head_`) and one consumer (ISR, reads `head_`, writes `tail_`). RX reverses the roles. One writer and one reader per index means no compare-exchange or critical section. Only `head_` and `tail_` are `volatile` — the buffer data itself is not, since ordering is guaranteed through the index writes. Usable capacity is 255 bytes: one slot stays unused to distinguish full from empty without a separate counter.

## Startup

No vendor startup file. `startup.cpp` contains the complete 98-entry vector table for the STM32F401xE (16 Cortex-M4 system exceptions + 82 device IRQs) and `Reset_Handler`. Every unused slot gets `__attribute__((weak, alias("Default_Handler")))`, which spins in `for(;;){}`. An unhandled interrupt halts at a known address a debugger can attach to.

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

`__libc_init_array()` runs before `main()`. `s_uart` in uart.cpp is a static object whose constructor must fire first to zero the ring buffer indices.

## Build

Toolchain targets Cortex-M4F: `-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard`. `-fno-exceptions -fno-rtti` strips C++ runtime overhead. `-ffunction-sections -fdata-sections` with `-Wl,--gc-sections` dead-strips anything unreachable from `Reset_Handler`. `-specs=nano.specs -specs=nosys.specs` links newlib-nano with stub syscalls. A custom `syscalls.cpp` overrides `_write` to route `printf` through the UART driver.

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=cmake/arm-none-eabi.cmake
cmake --build build
cmake --build build --target flash
```

The post-build steps produce an Intel HEX and a flat binary alongside the ELF. Flash with OpenOCD via the ST-Link on the Nucleo. Connect at 115200 8N1 on `/dev/tty.usbmodem*` (macOS) or `/dev/ttyACM0` (Linux). Every character echoes back, with `\r` expanded to `\r\n`.

---

[View on GitHub](https://github.com/HueCodes/UART-cpp)
