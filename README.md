# STM32 Fundamentals

A hands-on STM32 embedded systems learning project based on the [ControllersTech STM32 Beginner Course](https://controllerstech.com/stm32-beginner-course-learn-stm32-from-scratch/).

The primary target is the **NUCLEO-F446RE (STM32F446RE)**.

Selected earlier exercises were adapted to the **STM32F103C8T6 (Blue Pill)** and tested in **Wokwi** as an auxiliary simulation environment.

## Current Progress

### Clock Configuration

- System clock configured with STM32CubeMX.
- Primary STM32F446RE project configured for a 180 MHz system clock.
- A separate STM32F103C8T6 configuration is used for Wokwi simulation.

### GPIO

- Configured GPIO output with STM32 HAL.
- Implemented LED blinking using:
  - `HAL_GPIO_WritePin()`
  - `HAL_GPIO_TogglePin()`
- Worked with active-high and active-low LED configurations.
- Adapted the NUCLEO-F446RE PA5 LED example to the Blue Pill PC13 active-low LED for simulation.

### USART / UART

- Configured **USART2** on the primary STM32F446RE target with:
  - 115200 baud
  - 8 data bits
  - no parity
  - 1 stop bit
  - no hardware flow control
  - oversampling by 16
- Implemented polling-based UART transmission with `HAL_UART_Transmit()`.
- Implemented interrupt-driven UART transmission with `HAL_UART_Transmit_IT()`.
- Worked with UART transmission-complete callbacks using `HAL_UART_TxCpltCallback()`.
- Implemented UART transmission using DMA in normal mode with `HAL_UART_Transmit_DMA()`.
- Extended UART DMA transmission to circular mode with runtime buffer updates using `HAL_UART_TxHalfCpltCallback()` and `HAL_UART_TxCpltCallback()`.
- Configured DMA for USART2 TX on the STM32F446RE target.
- Transmitted text strings over UART.
- Converted integer values to text with `sprintf()` and transmitted them over UART.
- Implemented interrupt-driven UART reception with `HAL_UART_Receive_IT()`.
- Implemented reception of variable-length data by receiving one byte at a time and using `'\n'` as a message terminator.
- Implemented variable-length interrupt-driven reception using `HAL_UARTEx_ReceiveToIdle_IT()`.
- Configured UART4 for communication with an HC-05 Bluetooth module at 115200 baud, 8N1.
- Implemented bidirectional forwarding between USART2 and UART4:
  - PC → ST-LINK VCP → USART2 → UART4 → HC-05
  - HC-05 → UART4 → USART2 → ST-LINK VCP → PC
  - USART2 and UART4 use separate RX buffers.
  - `HAL_UARTEx_RxEventCallback()` stores the received size and sets a data-ready flag.
  - Forwarding is handled in the main loop.
  - Receive is re-armed after the current buffer has been forwarded.
- Physical HC-05 communication has not yet been validated.
- Connected USART2 to the Wokwi Serial Monitor for simulation.
- Used Wokwi Logic Analyzer / VCD output to inspect UART timing and inter-byte behavior.

### ADC

- Configured ADC1 for single-channel and multi-channel conversion on the STM32F446RE.
- Implemented single-channel ADC conversion using:
  - polling
  - interrupts
  - DMA
- Used `HAL_ADC_Start_IT()` and `HAL_ADC_ConvCpltCallback()` for interrupt-driven conversion.
- Used `HAL_ADC_Start_DMA()` for DMA-based ADC conversion.
- Implemented multi-channel ADC scanning with 4 regular conversions using DMA in Normal mode.
- Stored multi-channel ADC results in a `uint16_t` buffer and manually restarted DMA after transfer completion.
- Worked with ADC scan mode, conversion sequencing, sampling time, EOC, and DMA transfer configuration.

### I2C / LCD1602

- Configured I2C1 on the STM32F446RE:
  - PB6 — I2C1_SCL
  - PB7 — I2C1_SDA
  - Standard Mode at 100 kHz
  - Polling-based communication using STM32 HAL
- Studied I2C START/STOP conditions, addressing, ACK/NACK,
  and Open-Drain signaling.
- Implemented an LCD1602 driver using the HD44780 controller
  and PCF8574 I2C I/O expander.
- Implemented the following functions:
  - `pcf8574_write()` — I2C data transmission
  - `lcd_write_byte()` — 4-bit LCD data and command transmission
  - `lcd_init()` — LCD initialization
  - `lcd_send_string()` — string output
  - `lcd_put_cur()` — DDRAM cursor positioning
  - `lcd_send_int()` — integer output
  - `lcd_send_float()` — floating-point output
- Implemented LCD initialization, 4-bit data transmission,
  cursor positioning, and text formatting.
- Used `snprintf()` to convert numeric values into strings.
- LCD driver implementation is complete at the source-code level.
- Physical LCD1602/PCF8574 hardware testing is pending.

## Project Targets

| Purpose | Board / MCU | Notes |
|---|---|---|
| Primary development target | NUCLEO-F446RE / STM32F446RE | Main CubeMX/CubeIDE project |
| Simulation target | Blue Pill / STM32F103C8T6 | Used for selected earlier Wokwi exercises and retained as a reference |

Because the boards are electrically different, the simulation project is adapted where necessary rather than treated as a byte-for-byte copy of the primary project.

For example, the NUCLEO-F446RE user LED is active-high on PA5, while the Blue Pill LED on PC13 is active-low.

## Tools

- STM32CubeMX
- STM32CubeIDE
- STM32 HAL
- Wokwi
- Git / GitHub
- VSCode

## Repository Structure

```text
.
├── Core/                       # Application source and headers
├── Drivers/                    # STM32 HAL and CMSIS drivers
├── Videos/                     # Project demonstrations
├── .settings/                  # STM32CubeIDE settings
├── STM32_Fundamentals.ioc      # STM32CubeMX configuration
├── STM32F446RETX_FLASH.ld      # Flash linker script
├── STM32F446RETX_RAM.ld        # RAM linker script
├── .project                    # Eclipse/STM32CubeIDE project metadata
├── .cproject                   # C/C++ build configuration used by STM32CubeIDE
├── .mxproject                  # STM32CubeMX project metadata
└── .gitignore
```

Build output directories such as `Debug/` and `Release/` are intentionally excluded from Git.

## Build

1. Open `STM32_Fundamentals.ioc` in STM32CubeMX or STM32CubeIDE.
2. Generate the project code if the CubeMX configuration has changed.
3. Build the project in STM32CubeIDE.
4. Build artifacts are generated locally and are not committed to the repository.
5. For floating-point formatting with `snprintf()` in newlib-nano,
   the linker option `-u _printf_float` may be required.

## Wokwi Testing

Selected exercises were reproduced in a separate STM32F103C8T6 project for Wokwi in VSCode.

For USART2 testing:

- `PA2` — USART2 TX
- `PA3` — USART2 RX
- Wokwi Serial Monitor was used for UART input/output testing.
- Wokwi Logic Analyzer and VCD captures were used to inspect UART timing and diagnose simulator-specific behavior.

### Simulation Limitations

Wokwi was useful for early functional testing of GPIO and basic UART behavior, but several simulator-specific differences were encountered as the exercises became more timing- and peripheral-dependent.

During interrupt-driven UART transmission testing, the configured baud rate was 115200 and the individual UART bit timing matched this value. However, VCD analysis showed additional idle gaps between transmitted UART frames. These gaps increased the total transfer duration compared with the expected hardware timing.

DMA-based UART transmission was implemented for the STM32F446RE target but could not be meaningfully validated in the STM32F103C8T6 Wokwi environment used for this project.

A further limitation was found while testing blocking UART receive. Initial VCD analysis showed that the Wokwi Serial Monitor transmitted the entered `hello` sequence at approximately 9600 baud while USART2 on the simulated STM32 was configured for 115200 baud.

USART2 was temporarily reconfigured to 9600 baud to eliminate the baud-rate mismatch. VCD analysis then confirmed that the `hello` waveform reached PA3 / USART2_RX with the expected timing, and debugging confirmed that the USART baud-rate configuration was correct.

Despite this, `HAL_UART_Receive()` did not complete and the received data did not progress through the simulated USART2 receive path.

This indicates a limitation or defect in the STM32F103 USART2 RX emulation used by Wokwi rather than an application-level baud-rate configuration issue.

Because multiple simulator-specific differences were encountered in UART timing, interrupt-driven transmission behavior, DMA support, Serial Monitor behavior, and USART2 receive emulation, Wokwi is no longer used as a validation environment for new peripheral exercises.

Existing Wokwi examples are retained as reference implementations and as a record of the earlier simulation work. Further UART, interrupt, DMA, and other timing-sensitive functionality will be validated on the physical NUCLEO-F446RE target when hardware testing becomes available.

The NUCLEO-F446RE remains the primary target of the project.

## Roadmap

This repository will grow as the STM32 beginner course progresses.

- [x] Clock configuration
- [x] GPIO output and LED blinking
- [x] USART2 configuration
- [x] UART polling transmission
- [x] String and integer transmission
- [x] Wokwi UART/LED simulation
- [x] UART interrupt transmission
- [x] UART receive
- [x] UART DMA
- [x] UART Receive-to-Idle reception
- [x] USART2 ↔ UART4 bidirectional bridge for HC-05 Bluetooth module
- [x] ADC
  - [x] Single-channel polling
  - [x] Single-channel interrupt and DMA
  - [x] Multiple-channel DMA normal mode
- [x] I2C LCD1602 / PCF8574
  - [x] I2C1 configuration
  - [x] LCD1602 driver implementation
  - [x] String, integer, and float output functions
- [ ] DS3231 RTC
- [ ] BMP180
- [ ] BME280
- [ ] ST7735 TFT
- [ ] SD Card using SPI
- [ ] PWM with and without DMA
- [ ] WS2812 LED
- [ ] Passive buzzer
- [ ] HC-SR04

## Status

**Work in progress.**
The goal of this repository is not only to reproduce course examples, but to understand the underlying STM32 peripherals, clocking, GPIO behavior, communication interfaces, interrupt-driven operation, and HAL implementation while keeping the project reproducible in Git.