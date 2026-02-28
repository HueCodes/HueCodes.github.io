---
layout: hardware
title: STM32 Bare-Metal Development
status: in progress
github:
---

Programming STM32 microcontrollers at the register level without HAL or RTOS. Direct hardware access, timing-critical code, and peripheral configuration from scratch.

## Hardware

- **Board:** STM32F4 Discovery / Nucleo-F401RE
- **Core:** ARM Cortex-M4F @ 84-168MHz, hardware FPU
- **Memory:** 512KB-1MB Flash, 128-192KB SRAM
- **Debug:** ST-Link onboard, SWD + UART virtual COM

## Why Bare-Metal

The STM32 HAL works. It also generates 20,000+ lines before you write one of your own. Replacing the relevant portions with direct register access takes 200-400 lines. Every decision is visible, every clock enable and pin mode has a reason. Understanding what the HAL does — and when to use it — is more useful than never having seen the registers.

The cost: initial setup is slower. You need to read the reference manual, not just drag-and-drop in CubeMX. The benefit: bugs are easier to find because there is no generated code hiding between your logic and the hardware.

## Clocks

The F401RE uses an 8MHz HSE from the ST-Link. Target SYSCLK is 84MHz via the main PLL:

```c
// HSE=8MHz -> /8 (M) -> 1MHz VCO_in
// 1MHz * 336 (N) -> 336MHz VCO_out
// 336MHz / 4 (P) -> 84MHz SYSCLK
RCC->PLLCFGR = (8u << RCC_PLLCFGR_PLLM_Pos)
             | (336u << RCC_PLLCFGR_PLLN_Pos)
             | (1u << RCC_PLLCFGR_PLLP_Pos)   // P=4 (bits=01)
             | RCC_PLLCFGR_PLLSRC_HSE
             | (7u << RCC_PLLCFGR_PLLQ_Pos);  // USB=48MHz
```

Flash latency must be set before switching the clock source. At 84MHz, 2 wait states. Switch sequence: set wait states, enable PLL, wait for PLLRDY, set SW=PLL, confirm SWS.

## GPIO and Alternate Functions

Every peripheral pin goes through three steps: clock enable on AHB1, MODER set to alternate function (0b10), and AFR set to the correct AF number from the datasheet.

```c
RCC->AHB1ENR  |= RCC_AHB1ENR_GPIOAEN;
GPIOA->MODER  |= (2u << (2 * 2));  // PA2: alternate function
GPIOA->AFR[0] |= (7u << (4 * 2));  // PA2: AF7 (USART2_TX)
```

Missing the clock enable produces no error — the register write is silently ignored and the peripheral never initializes.

## UART

Interrupt-driven UART driver with SPSC ring buffers. No blocking, no busy-wait. Details in the [blog post](/blog/bare-metal-uart-stm32/). Source on [GitHub](https://github.com/HueCodes/UART-cpp).

Key points:
- BRR computed from actual APB1 frequency, not a hardcoded constant
- TXEIE enabled on-demand, cleared by the ISR when the TX buffer drains
- Volatile index semantics on the ring buffer avoid atomics in the ISR
- Error flags cleared by the SR+DR two-step read sequence per the F401 reference manual

## Timers and PWM

General-purpose timers (TIM2-TIM5) run off APB1. TIM1 runs off APB2. PWM uses output compare mode with the timer running in up-count. The PWM frequency is `PCLK / (PSC+1) / (ARR+1)`. Duty cycle is `CCR / ARR`.

```c
// TIM2 CH1 PWM at 1kHz, 50% duty, PCLK=42MHz
TIM2->PSC = 41;       // /42 -> 1MHz timer clock
TIM2->ARR = 999;      // /1000 -> 1kHz
TIM2->CCR1 = 500;     // 50%
TIM2->CCMR1 = (6u << TIM_CCMR1_OC1M_Pos) | TIM_CCMR1_OC1PE;  // PWM mode 1
TIM2->CCER  = TIM_CCER_CC1E;
TIM2->CR1   = TIM_CR1_CEN;
```

## ADC

The F401 ADC runs at up to 36MHz (APB2 / prescaler). Scan mode reads multiple channels sequentially into a buffer. DMA mode eliminates CPU involvement in data transfer — the ADC fills a buffer in the background and raises an interrupt when complete.

Sampling time matters. High-impedance sources need longer sampling (144 cycles minimum). The ADC input capacitor charges through the source impedance; too short a sample and you read the previous channel's voltage.

## SPI

SPI is straightforward once the clock polarity (CPOL) and phase (CPHA) match the peripheral datasheet. Most sensors use mode 0 (CPOL=0, CPHA=0) or mode 3 (CPOL=1, CPHA=1).

```c
SPI1->CR1 = SPI_CR1_MSTR           // master
          | SPI_CR1_SSM             // software NSS
          | SPI_CR1_SSI
          | (2u << SPI_CR1_BR_Pos)  // PCLK/8
          | SPI_CR1_SPE;
```

DMA-driven SPI transfers avoid blocking while the peripheral shifts out bits. Useful for display drivers (SSD1306 over SPI) and sensor bursts.

## I2C

The most finicky STM32 peripheral. The clock speed register (CCR) must match the APB1 frequency. Standard mode is 100kHz; fast mode is 400kHz. The start condition, address byte, ACK check, data bytes, and stop condition each require polling the appropriate status bits.

```c
// Generate start
I2C1->CR1 |= I2C_CR1_START;
while (!(I2C1->SR1 & I2C_SR1_SB));  // wait for SB flag

// Send address (7-bit, write)
I2C1->DR = (addr << 1);
while (!(I2C1->SR1 & I2C_SR1_ADDR));
(void)I2C1->SR2;  // reading SR2 clears ADDR
```

The F4 I2C has a known errata around clock stretching. The recommended workaround is to disable PE and re-enable before each transaction.

## Interrupts and NVIC

NVIC priority on Cortex-M4 uses 4 bits by default, giving 16 levels. Lower numbers preempt higher numbers. FreeRTOS reserves priorities above `configMAX_SYSCALL_INTERRUPT_PRIORITY` (default: 5) for ISRs that call FreeRTOS API functions. ISRs at priority 0-4 cannot safely call any FreeRTOS function.

Bare-metal without FreeRTOS: set peripheral ISR priorities above SysTick (priority 15 for SysTick by convention). For UART: priority 5-8. For time-critical GPIO: priority 2-4.

## DMA

DMA is the right answer when peripherals produce or consume data faster than interrupt overhead allows. ADC at full speed (1Msps), SPI display updates, and UART RX at high baud rates all benefit from DMA.

Each DMA stream has a channel, direction, memory/peripheral address, data size, and a transfer-complete interrupt. The memory address increments; the peripheral address stays fixed.

## Debugging

OpenOCD + GDB over ST-Link SWD. A `.gdbinit` sets `monitor reset halt`, loads the ELF, and runs to `main`. CMSIS core registers map cleanly in GDB — you can inspect `$sp`, watch peripheral registers via `x/4wx 0x40011000` (GPIOA), and set hardware breakpoints.

For hard faults: the Cortex-M4 HardFault handler can capture `LR`, `PC`, and `PSR` from the exception stack frame and print them over UART before halting.
