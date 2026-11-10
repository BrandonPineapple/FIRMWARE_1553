
# Pinout

**FIRMWARE_1553** / Hardware Documentation

![MCU](https://img.shields.io/badge/MCU-STM32F407-03234B?style=flat-square)
![Controller](https://img.shields.io/badge/Controller-COM1553B-526477?style=flat-square)
![Mode](https://img.shields.io/badge/Mode-Remote_Terminal-526477?style=flat-square)
![Status](https://img.shields.io/badge/Status-In_Development-D69E2E?style=flat-square)

**Development Board:** STM32F407G-DISC1  
**Microcontroller:** STM32F407VGT6  
**Communication Standard:** MIL-STD-1553B  
**Document Revision:** 0.1

---

## 1. Overview

This document defines the signal configuration and pin assignments between the **STM32F407VGT6** microcontroller and the **COM1553B** MIL-STD-1553B controller.

The STM32F407 acts as the host processor and manages the COM1553B through a 16-bit bidirectional parallel data bus, control signals, and status monitoring.

### 1.1 Configuration Status

| Status | Description |
|:-------|:------------|
| Configured | Assigned and configured in STM32CubeMX |
| Pending | Pin assignment or configuration not yet defined |
| Pending Validation | Configured but not experimentally verified |
| Verified | Confirmed through hardware testing |

A configuration is considered verified only after the corresponding hardware test has been completed successfully.

---


## 2. COM1553B Signal Classification

This section groups the COM1553B interface signals by functionality.

The classification is based on the COM1553B datasheet and the existing firmware interface developed during preliminary testing.

Signal directions for host-interface signals are defined with respect to the STM32F407.

### 2.1 Parallel Data Bus

The COM1553B provides a 16-bit bidirectional data bus.

| Signal | Direction | Description |
|:-------|:----------|:------------|
| D0 | Bidirectional | Data bit 0 |
| D1 | Bidirectional | Data bit 1 |
| D2 | Bidirectional | Data bit 2 |
| D3 | Bidirectional | Data bit 3 |
| D4 | Bidirectional | Data bit 4 |
| D5 | Bidirectional | Data bit 5 |
| D6 | Bidirectional | Data bit 6 |
| D7 | Bidirectional | Data bit 7 |
| D8 | Bidirectional | Data bit 8 |
| D9 | Bidirectional | Data bit 9 |
| D10 | Bidirectional | Data bit 10 |
| D11 | Bidirectional | Data bit 11 |
| D12 | Bidirectional | Data bit 12 |
| D13 | Bidirectional | Data bit 13 |
| D14 | Bidirectional | Data bit 14 |
| D15 | Bidirectional | Data bit 15 |

The host processor must support input and output operation.

GPIO direction shall be configured according to the COM1553B transfer cycle.

> [!IMPORTANT]
> The parallel interface must prevent bus contention.
>
> The STM32F407 and COM1553B must never drive the same data lines simultaneously.

### 2.2 Clock and Configuration

| Signal | Direction | Function |
|:-------|:----------|:---------|
| CLK | STM32 → COM1553B | 12 MHz reference clock |
| POR | STM32 → COM1553B | Controller reset |
| RT/BC | STM32 → COM1553B | Operating mode selection |

**Configuration requirements:**

- The COM1553B requires a 12 MHz reference clock.
- `POR` is used to reset the controller.
- `RT/BC` selects the required operating mode.
- The selected operating mode is Remote Terminal.
- Reset and startup timing must comply with the controller datasheet.

### 2.3 Control Signals

| Signal | Direction | Function |
|:-------|:----------|:---------|
| CSTR | STM32 → COM1553B | Command Strobe |
| BGACK | STM32 → COM1553B | Bus Grant Acknowledge |
| DTACK | STM32 → COM1553B | Data Transfer Acknowledge |

These signals coordinate host control operations and data transfers.

The firmware must manage their assertion and release according to the COM1553B timing requirements.

### 2.4 Handshake Signals

| Signal | Direction | Function |
|:-------|:----------|:---------|
| CSTRA | COM1553B → STM32 | Command Strobe Acknowledge |
| DTR | COM1553B → STM32 | Data Transfer Request |

These signals participate in the handshake between the controller and host processor.

The final firmware design will evaluate interrupt-driven detection to support time-critical operations.

### 2.5 Status and Monitoring Signals

| Signal | Planned Direction | Function |
|:-------|:------------------|:---------|
| R/W | Input | Read/write operation indication |
| WE | Input | Write Enable |
| C/D | Input | Command/Data indication |
| TXMODE | Input | Transmission mode indication |
| MC | Input | Message Complete |
| IM | Input | Invalid Message |
| MCF | Input | Mode Code Flag |
| VC | Input | Valid Command indication |

These signals support:

- Identification of transfer operations.
- Monitoring of controller activity.
- Message completion detection.
- Error and diagnostic reporting.
- Communication event handling.

The electrical direction and timing of each signal must be confirmed against the final COM1553B pin-function table before implementing the STM32 GPIO configuration.

### 2.6 Manchester Interface

The COM1553B uses a dedicated Manchester interface to communicate with the external MIL-STD-1553B transceiver.

| Signal | COM1553B Direction | Function |
|:-------|:-------------------|:---------|
| MANIN | Input | Manchester receive input |
| Complementary MANIN | Input | Complementary receive input |
| MANOUT | Output | Manchester transmit output |
| Complementary MANOUT | Output | Complementary transmit output |

These signals connect to the external transceiver rather than the STM32 host GPIO interface.

### 2.7 Additional Hardware Signals

The complete schematic must also account for:

- Controller power and ground connections.
- Transceiver interface signals.
- Hardware configuration inputs.
- Fixed-level control inputs.
- Reserved or unused pins.

Power distribution is handled separately from this document.

---

## 3. STM32 Pin Assignment

This section defines the physical correspondence between COM1553B signals and STM32F407 GPIO pins.

The final assignments will be implemented in STM32CubeMX.

### 3.1 Clock and Configuration

| COM1553B Signal | STM32 Pin | Configuration | Status |
|:----------------|:----------|:--------------|:-------|
| CLK | PE9 | TIM1_CH1 / PWM | Pending Validation |
| POR | TBD | GPIO Output | Pending |
| RT/BC | TBD | GPIO Output | Pending |

**Current assignment:**

`COM1553_CLK → PE9 → TIM1_CH1`

PE9 is reserved for generating the 12 MHz COM1553B clock.

### 3.2 Control and Handshake

| COM1553B Signal | STM32 Pin | GPIO Configuration | Status |
|:----------------|:----------|:-------------------|:-------|
| CSTR | TBD | GPIO Output | Pending |
| BGACK | TBD | GPIO Output | Pending |
| DTACK | TBD | GPIO Output | Pending |
| CSTRA | TBD | GPIO Input / EXTI candidate | Pending |
| DTR | TBD | GPIO Input / EXTI candidate | Pending |

The interrupt configuration will be determined after reviewing the required timing and available EXTI lines.

### 3.3 Parallel Data Bus

| COM1553B Signal | STM32 Pin | GPIO Configuration | Status |
|:----------------|:----------|:-------------------|:-------|
| D0 | TBD | Bidirectional GPIO | Pending |
| D1 | TBD | Bidirectional GPIO | Pending |
| D2 | TBD | Bidirectional GPIO | Pending |
| D3 | TBD | Bidirectional GPIO | Pending |
| D4 | TBD | Bidirectional GPIO | Pending |
| D5 | TBD | Bidirectional GPIO | Pending |
| D6 | TBD | Bidirectional GPIO | Pending |
| D7 | TBD | Bidirectional GPIO | Pending |
| D8 | TBD | Bidirectional GPIO | Pending |
| D9 | TBD | Bidirectional GPIO | Pending |
| D10 | TBD | Bidirectional GPIO | Pending |
| D11 | TBD | Bidirectional GPIO | Pending |
| D12 | TBD | Bidirectional GPIO | Pending |
| D13 | TBD | Bidirectional GPIO | Pending |
| D14 | TBD | Bidirectional GPIO | Pending |
| D15 | TBD | Bidirectional GPIO | Pending |

**Design objective:**

Where practical, data bus signals should be assigned to the same STM32 GPIO port or grouped into a small number of ports.

This arrangement will simplify:

- Parallel data acquisition.
- Parallel data output.
- GPIO direction switching.
- Bit masking and reconstruction.
- Data bus driver implementation.

### 3.4 Status and Monitoring

| COM1553B Signal | STM32 Pin | Planned Configuration | Status |
|:----------------|:----------|:----------------------|:-------|
| R/W | TBD | GPIO Input | Pending |
| WE | TBD | GPIO Input | Pending |
| C/D | TBD | GPIO Input | Pending |
| TXMODE | TBD | GPIO Input / EXTI candidate | Pending |
| MC | TBD | GPIO Input / EXTI candidate | Pending |
| IM | TBD | GPIO Input / EXTI candidate | Pending |
| MCF | TBD | GPIO Input | Pending |
| VC | TBD | GPIO Input | Pending |

All GPIO modes shown above are preliminary until the signal directions and timing are confirmed.

---

## 4. Signal Configuration Requirements

### 4.1 GPIO Outputs

Control outputs must be initialized to defined inactive states before communication begins.

The initialization sequence must avoid unintended:

- Controller resets.
- Control strobes.
- Transfer acknowledgments.
- Bus conflicts.

### 4.2 GPIO Inputs

Input signals must use suitable pull configurations based on their electrical characteristics and the external interface circuitry.

Input pull-up and pull-down requirements must be established from the hardware schematic and datasheet.

### 4.3 Bidirectional Data Bus

The firmware must explicitly manage the parallel data bus direction.

A general transfer sequence will involve:

1. Determine the transfer direction.
2. Configure the STM32 data GPIOs.
3. Perform the required data read or write operation.
4. Generate or acknowledge the appropriate handshake.
5. Release or restore the bus to its required state.

The exact signal ordering and timing must follow the COM1553B datasheet.

### 4.4 Logic-Level Compatibility

The STM32F407 and COM1553B operate in different logic-voltage domains.

The external hardware interface must provide the required compatibility.

The firmware pin configuration must reflect the actual interface circuit and signal direction.

---

## 5. References

The following documents are used as technical references:

1. **COM1553B SMART Controller Datasheet** — Pin configuration, signal description, and timing requirements.
2. **STM32F407 Reference Manual (RM0090)** — GPIO, timers, EXTI, and peripheral configuration.
3. **STM32F407G-DISC1 User Manual (UM1472)** — Board connections and hardware resources.
4. **STM32CubeMX Documentation** — Microcontroller pin and clock configuration.

---

## 6. Revision History

| Revision | Description |
|:---------|:------------|
| 0.1 | Initial COM1553B signal classification, preliminary STM32 pinout, and clock configuration |

---

**Project:** FIRMWARE_1553  
**Document:** Pinout  
**Operating Mode:** Remote Terminal (RT)  
**Status:** Preliminary Design
