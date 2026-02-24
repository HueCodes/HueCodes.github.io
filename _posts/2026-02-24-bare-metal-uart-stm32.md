---
layout: post
title: "Bare-Metal UART on STM32F4 Without the HAL"
date: 2026-02-24
category: projects
---

Interrupt-driven UART driver for the STM32F401xE Nucleo in C++17. No HAL, no RTOS, no heap.

![UART-Cpp architecture diagram](/assets/images/projects/uart-cpp-architecture.svg)

CubeMX and the ST HAL work. They also generate 20,000 lines of code before you write a single line of your own. Replacing all of it with direct register access takes about 200 lines and makes every decision visible. The full source is on [GitHub](https://github.com/HueCodes/UART-cpp).

## Clocks

The Nucleo provides an 8 MHz external oscillator (HSE). Target is 84 MHz SYSCLK via the main PLL.

```cpp
// SYSCLK = 8 / 8 * 336 / 4 = 84 MHz
RCC->PLLCFGR = (8u   << RCC_PLLCFGR_PLLM_Pos)
             | (336u << RCC_PLLCFGR_PLLN_Pos)
             | (1u   << RCC_PLLCFGR_PLLP_Pos)  // 01 -> /4
             | RCC_PLLCFGR_PLLSRC_HSE
             | (7u   << RCC_PLLCFGR_PLLQ_Pos);
```

One ordering constraint matters here. Flash latency must be set to 2 wait states before switching SYSCLK to the PLL. At 3.3 V and 84 MHz, the CPU reads corrupted instructions if you switch the clock source first.

```cpp
FLASH->ACR = FLASH_ACR_PRFTEN | FLASH_ACR_ICEN | FLASH_ACR_DCEN | 2u;
```

Set latency, then enable the PLL, then switch. APB1 runs at SYSCLK/2 (42 MHz) because its 45 MHz maximum is below SYSCLK. USART2 sits on APB1, which matters for baud rate.

## GPIO and USART2 Init

The Nucleo routes USART2 through the on-board ST-Link to the host machine as a virtual COM port. The pin mapping is fixed by hardware:

- PA2 = USART2_TX (AF7)
- PA3 = USART2_RX (AF7)

Init sequence:

1. Enable GPIOA and USART2 clocks on AHB1 and APB1.
2. Read APB1ENR once to let the enable propagate before accessing GPIO registers.
3. Set PA2 and PA3 to alternate function mode (MODER = 10), push-pull, high speed, no pull, AF7.
4. Disable USART2 (clear UE), configure data bits, parity, and stop bits.
5. Write BRR.
6. Enable RXNEIE, then TE, RE, and UE.
7. Set NVIC priority and enable the USART2 IRQ.

Baud rate is calculated at runtime from the actual APB1 clock, not a hardcoded constant.

```cpp
uint32_t ppre1 = (RCC->CFGR >> RCC_CFGR_PPRE1_Pos) & 0x7u;
uint32_t div   = (ppre1 < 4u) ? 1u : (1u << (ppre1 - 3u));
uint32_t pclk1 = SystemCoreClock / div;
USART2->BRR = (pclk1 + baud_ / 2u) / baud_;
```

## Interrupt-Driven Design

Polling TXE and RXNE directly burns CPU time waiting on peripheral timing. Both directions use interrupts instead.

**RX**: RXNEIE is always enabled after init. Every received byte gets pushed into a ring buffer inside the ISR. The application calls `receive()`, which spins until the buffer has data, or polls with `rx_ready()` for non-blocking use.

**TX**: TXEIE is off at startup. When the application calls `send()`, it pushes a byte into the TX ring buffer, then enables TXEIE. The ISR fires when TXE is set, pops a byte from the buffer, and writes it to DR. When the buffer drains, the ISR disables TXEIE itself.

```cpp
void Uart::send(uint8_t byte)
{
    while (!tx_buf_.push(byte)) {}
    USART2->CR1 |= USART_CR1_TXEIE;
}
```

The on-demand enable/disable pattern prevents the ISR from firing continuously when there is nothing to send.

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

The ISR is declared `extern "C"` to prevent C++ name mangling. Without it the linker resolves the vector slot to `Default_Handler` instead.

## Ring Buffer

Both TX and RX use a power-of-two SPSC ring buffer.

```cpp
template <typename T, size_t N>
class RingBuffer {
    static_assert((N & (N - 1)) == 0, "RingBuffer size must be a power of 2");
    static constexpr size_t MASK = N - 1;

    volatile size_t head_{0};
    volatile size_t tail_{0};
    T buf_[N];
    ...
};
```

The SPSC property is what makes this lock-free. For TX: only the application writes `head_` (in `push`), only the ISR reads it and writes `tail_` (in `pop`). For RX the roles reverse. One writer and one reader per index means no atomics or critical sections are needed.

Only `head_` and `tail_` are `volatile`. The buffer data itself does not need to be, since a happens-before relationship exists through the index updates.

Both buffers are 256 bytes. Power-of-two sizing replaces modulo with a bitmask. Usable capacity is 255 bytes because the full/empty distinction requires one slot to stay unused.

## Error Handling

STM32F4 USART error flags (ORE, FE, NE) require a two-step sequence to clear: read SR, then read DR. The driver calls `get_and_clear_errors()` at the top of every ISR invocation before checking RXNE and TXE. Error flags are always cleared and the errored byte consumed before the rest of the ISR logic runs.

## Startup

There is no vendor startup file. `startup.cpp` provides the full vector table for the STM32F401xE (16 ARM Cortex-M4 entries plus 82 device IRQs) and `Reset_Handler`.

```cpp
extern "C" void Reset_Handler()
{
    uint32_t* src = &_sidata;
    uint32_t* dst = &_sdata;
    while (dst < &_edata) { *dst++ = *src++; }

    dst = &_sbss;
    while (dst < &_ebss) { *dst++ = 0; }

    __libc_init_array();

    main();
    for (;;) {}
}
```

All handlers not explicitly defined get `__attribute__((weak, alias("Default_Handler")))`. Unhandled interrupts spin in place, which gives a debugger something to attach to rather than jumping to an undefined address.

## Build

CMake with an arm-none-eabi-gcc toolchain file. Key flags:

```
-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard
-fno-exceptions -fno-rtti -ffunction-sections -fdata-sections -Os
-specs=nano.specs -specs=nosys.specs -Wl,--gc-sections
```

`-specs=nano.specs` links against newlib-nano. A custom `syscalls.cpp` overrides `_write` to route `printf` through the UART.

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=cmake/arm-none-eabi.cmake
cmake --build build
cmake --build build --target flash
```

Connect at 115200 8N1 on the virtual COM port. Every character typed echoes back, with `\r` expanded to `\r\n`.

## What is Missing

The driver covers basic use. For production contexts:

- DMA transfer for high-throughput TX without per-byte interrupts
- Hardware flow control (RTS/CTS) for slow receivers
- Receive timeout to avoid blocking indefinitely on a lost packet

Source: [github.com/HueCodes/UART-cpp](https://github.com/HueCodes/UART-cpp)

---
