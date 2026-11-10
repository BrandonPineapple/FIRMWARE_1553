
# FIRMWARE_1553

**MIL-STD-1553B Remote Terminal Firmware**

![MCU](https://img.shields.io/badge/MCU-STM32F407-03234B?style=flat-square)
![Controller](https://img.shields.io/badge/Controller-COM1553B-526477?style=flat-square)
![Mode](https://img.shields.io/badge/Mode-Remote_Terminal-526477?style=flat-square)
![Status](https://img.shields.io/badge/Status-In_Development-D69E2E?style=flat-square)

---

## Overview

Embedded firmware for MIL-STD-1553B communication control and management using the **COM1553B controller** operating in **Remote Terminal (RT)** mode with an STM32 microcontroller.

## Hardware

| Component | Device |
|:----------|:-------|
| Microcontroller | STM32F407VGT6 |
| Development Board | STM32F407G-DISC1 |
| MIL-STD-1553B Controller | COM1553B |
| Operating Mode | Remote Terminal (RT) |
| Development Tools | STM32CubeMX / STM32CubeIDE |

## Firmware Architecture

The firmware will include:

- Hardware abstraction and peripheral initialization.
- COM1553B interface drivers.
- Interrupt-driven communication handling.
- Remote Terminal command and data processing.
- Error detection and diagnostic mechanisms.

## Documentation

Technical documentation, hardware configurations, interface specifications, and test procedures will be maintained in the `docs/` directory.

- [Pinout and Signal Configuration](docs/hardware/pinout.md)

## Development Environment

- **STM32CubeMX:** Peripheral and pin configuration.
- **STM32CubeIDE:** Firmware development and debugging.
- **STM32 HAL:** Hardware abstraction library.
- **Git / GitHub:** Version control and technical documentation.

---

**Project Status:** In Development

