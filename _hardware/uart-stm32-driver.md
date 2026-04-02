---
layout: hardware
title: STM32 Bare-Metal UART Driver
status: complete
github:
---

Bare-metal UART driver for the **STM32F401** in C++17. No HAL, no RTOS, no heap. Interrupt-driven TX and RX with lock-free SPSC ring buffers, full startup file and linker script, clock init (PLL to 84 MHz), and 47 Catch2 host tests.

## Architecture

```
Application
    |
    v
send() / receive()           -- non-blocking TX, blocking RX (1ms SysTick)
    |           |
    v           v
TX Ring Buffer (256B)     RX Ring Buffer (256B)
    |  SPSC volatile-index     |  SPSC volatile-index
    v                          v
USART2 TXE ISR            USART2 RXNE ISR
    |                          |
    v                          v
PA2 (TX)                   PA3 (RX)  -- routed to ST-Link VCP
```

## Why Interrupt-Driven

At 115200 baud, one byte takes ~87 us. Polling wastes that entire time spinning in `while (!(SR & TXE))`. Interrupt-driven TX returns immediately from `send()` -- the ISR drains the ring buffer autonomously. At 1 Mbaud (10 us/byte), polling would consume ~8% of an 84 MHz core just on serial output.

## Ring Buffer Design

SPSC (single-producer, single-consumer) with `volatile` head/tail indices. For TX: `send()` is the only producer, the ISR is the only consumer. For RX: the ISR is the only producer, `receive()` is the only consumer.

No mutex needed. On ARMv7-M, naturally-aligned 32-bit loads/stores are atomic with respect to interrupts, so `volatile` alone is sufficient.

## Error Handling

USART errors (ORE, FE, NE) are cleared by the two-step read-SR-then-read-DR sequence. ORE must be cleared before checking RXNE -- otherwise the ORE flag keeps the interrupt pending, causing an infinite ISR loop.

## Clock Configuration

HSE 8 MHz crystal through the PLL: M=4, N=168, P=4 gives SYSCLK = 84 MHz. APB1 at 42 MHz, APB2 at 84 MHz. All done in register-level init before main().

## Testing

47 Catch2 host tests compile against mock CMSIS headers -- no hardware needed. Tests cover ring buffer edge cases, UART configuration, error flag handling, and clock setup validation.

```bash
mkdir build_host && cd build_host
cmake -DBUILD_HOST_TESTS=ON ..
make && ctest
```
