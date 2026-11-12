
# Pinout

**FIRMWARE_1553** / Hardware Documentation

![MCU](https://img.shields.io/badge/MCU-STM32F407-03234B?style=flat-square)
![Controller](https://img.shields.io/badge/Controller-COM1553B-526477?style=flat-square)
![Mode](https://img.shields.io/badge/Mode-Remote_Terminal-526477?style=flat-square)
![Status](https://img.shields.io/badge/Status-Pending_Validation-D69E2E?style=flat-square)

**Development Board:** STM32F407G-DISC1  
**Microcontroller:** STM32F407VGT6  
**Communication Controller:** COM1553B SMART  
**Communication Standard:** MIL-STD-1553B  
**Operating Mode:** Remote Terminal (RT)  

---

## 1. Overview

This document defines the signal configuration and pin assignments between the **STM32F407VGT6** microcontroller and the **COM1553B** MIL-STD-1553B controller.

The STM32F407 acts as the host processor and manages the COM1553B through a 16-bit bidirectional parallel data bus, digital control signals, handshake operations, and status monitoring.

The COM1553B is intended to operate as a **Remote Terminal (RT)**.

The initial microcontroller configuration has been completed using STM32CubeMX. The firmware project has been generated and successfully compiled using STM32CubeIDE.

The configuration includes:

- A 16-bit bidirectional parallel data bus.
- A 12 MHz reference clock generated using TIM1.
- Controller reset and operating mode selection.
- Digital control and handshake signals.
- Status and monitoring inputs.
- External interrupts for communication events.

The configuration described in this document represents the current firmware baseline.

Hardware validation and functional communication tests remain pending.

### 1.1 Configuration Status

| Status | Description |
|:-------|:------------|
| Configured | Assigned and configured in STM32CubeMX |
| Pending | Assignment or configuration not yet defined |
| Pending Validation | Configured but not experimentally verified |
| Verified | Confirmed through hardware testing |

A signal is considered verified only after its corresponding hardware test has been completed successfully.

Successful code generation or compilation does not constitute hardware validation.

**Current Status:** Configured — Pending Hardware Validation.

---

## 2. COM1553B Signal Classification

This section groups the COM1553B interface signals according to their functionality.

The classification is based on the COM1553B datasheet and the existing firmware interface developed during preliminary testing.

Unless otherwise specified, signal directions are defined with respect to the STM32F407.

### 2.1 Parallel Data Bus

The COM1553B provides a 16-bit bidirectional parallel data bus.

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

The STM32F407 must support both input and output operations.

All data bus signals are initially configured as GPIO inputs.

The firmware shall dynamically change their direction according to the COM1553B transfer cycle.

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
- `POR` controls the controller reset sequence.
- `RT/BC` determines the operating mode.
- The intended operating mode is Remote Terminal.
- Startup and reset timing must comply with the COM1553B datasheet.
- The reference clock must be validated before controller operation.

### 2.3 Control Signals

| Signal | Direction | Function |
|:-------|:----------|:---------|
| CSTR | STM32 → COM1553B | Command Strobe |
| BGACK | STM32 → COM1553B | Bus Grant Acknowledge |
| DTACK | STM32 → COM1553B | Data Transfer Acknowledge |

These signals coordinate controller operations and parallel data transfers.

Their assertion and release must follow the timing requirements defined in the COM1553B datasheet.

### 2.4 Handshake Signals

| Signal | Direction | Function |
|:-------|:----------|:---------|
| CSTRA | COM1553B → STM32 | Command Strobe Acknowledge |
| DTR | COM1553B → STM32 | Data Transfer Request |

The handshake interface coordinates communication between the COM1553B and the host processor.

The initial configuration uses standard GPIO inputs for `CSTRA` and `DTR`.

The firmware implementation must evaluate whether polling or interrupt-driven processing is appropriate for the required timing.

### 2.5 Status and Monitoring Signals

| Signal | STM32 Direction | Function |
|:-------|:----------------|:---------|
| R/W | Input | Read/Write operation indication |
| WE | Input | Write Enable |
| C/D | Input | Command/Data indication |
| TXMODE | Input | Transmission mode indication |
| MC | Input / EXTI | Message Complete |
| IM | Input / EXTI | Invalid Message |
| MCF | Input | Mode Code Flag |
| VC | Input | Valid Command indication |

These signals support:

- Identification of transfer operations.
- Monitoring of controller activity.
- Message completion detection.
- Error and diagnostic reporting.
- Communication event handling.

MC and IM are configured as external interrupt inputs.

The remaining monitoring signals are configured as standard GPIO inputs.

Signal polarity and timing must be validated before functional operation.

### 2.6 Control Bits CB0–CB2

The COM1553B interface uses control bits `CB0`, `CB1`, and `CB2` for specific control operations.

These bits are transferred using the existing parallel data bus and do not require independent STM32 GPIO assignments.

| Control Bit | Data Bus Signal | STM32 Pin |
|:------------|:----------------|:----------|
| CB0 | D0 | PE7 |
| CB1 | D1 | PE8 |
| CB2 | D2 | PE10 |

The firmware must implement the appropriate control encoding according to the COM1553B datasheet.

### 2.7 Manchester Interface

The COM1553B provides a dedicated Manchester interface for communication with the external MIL-STD-1553B transceiver.

| Signal | COM1553B Direction | Function |
|:-------|:-------------------|:---------|
| MANIN | Input | Manchester receive input |
| Complementary MANIN | Input | Complementary receive input |
| MANOUT | Output | Manchester transmit output |
| Complementary MANOUT | Output | Complementary transmit output |

These signals connect to the external transceiver rather than the STM32 host GPIO interface.

### 2.8 Additional Hardware Signals

The complete hardware schematic must also account for:

- Controller power and ground connections.
- Transceiver interface signals.
- Hardware configuration inputs.
- Fixed-level control inputs.
- Reserved and unused pins.

Power distribution and external signal conditioning are handled separately by the hardware design.

---

## 3. STM32 Pin Assignment

This section defines the current signal correspondence between the COM1553B and STM32F407.

The assignments have been implemented in the `1553_DISCO` STM32CubeMX project.

All assignments remain subject to hardware validation.

### 3.1 Clock and Configuration

| COM1553B Signal | STM32 Pin | Configuration | Initial State | Status |
|:----------------|:----------|:--------------|:--------------|:-------|
| CLK | PE9 | TIM1_CH1 / PWM | Timer controlled | Pending Validation |
| POR | PB12 | GPIO Output Push Pull | High | Pending Validation |
| RT/BC | PC6 | GPIO Output Push Pull | High | Pending Validation |

**Clock Assignment:**

`COM1553_CLK → PE9 → TIM1_CH1`

PE9 is reserved exclusively for generating the COM1553B reference clock.

#### 3.1.1 TIM1 Configuration

| Parameter | Value |
|:----------|:------|
| Timer | TIM1 |
| Channel | CH1 |
| Output Pin | PE9 |
| Clock Source | Internal Clock |
| Timer Input Clock | 168 MHz |
| Mode | PWM Generation CH1 |
| Prescaler | 0 |
| Counter Mode | Up |
| Counter Period | 13 |
| Pulse | 7 |
| PWM Mode | PWM Mode 1 |
| Output Polarity | High |
| Expected Frequency | 12 MHz |
| Duty Cycle | 50% |

The PWM output frequency is calculated using:

```text
f_PWM = f_TIM / [(PSC + 1) × (ARR + 1)]

f_PWM = 168,000,000 / [(0 + 1) × (13 + 1)]

f_PWM = 12,000,000 Hz
```

The resulting period is approximately:

```text
T = 1 / 12,000,000

T = 83.33 ns
```

The expected duty cycle is:

```text
Duty Cycle = 7 / 14 × 100 = 50%
```

The timer is configured during initialization through:

```c
MX_TIM1_Init();
```

However, timer initialization does not automatically start PWM generation.

The firmware must explicitly start the output using:

```c
HAL_TIM_PWM_Start(&htim1, TIM_CHANNEL_1);
```

> [!NOTE]
> The 12 MHz PWM configuration has been generated and compiled successfully.
>
> Physical output verification using an oscilloscope remains pending.

#### 3.1.2 Reset Configuration

| Parameter | Value |
|:----------|:------|
| COM1553B Signal | POR |
| STM32 Pin | PB12 |
| User Label | `COM_POR` |
| GPIO Mode | Output Push Pull |
| GPIO Output Level | High |
| GPIO Pull-up/Pull-down | No Pull |
| Maximum Output Speed | Low |

The GPIO is initialized to High.

The firmware must implement the required reset sequence according to the COM1553B datasheet.

#### 3.1.3 Operating Mode Configuration

| Parameter | Value |
|:----------|:------|
| COM1553B Signal | RT/BC |
| STM32 Pin | PC6 |
| User Label | `COM_RTBC` |
| GPIO Mode | Output Push Pull |
| GPIO Output Level | High |
| GPIO Pull-up/Pull-down | No Pull |
| Maximum Output Speed | Low |

The project is intended to operate in Remote Terminal mode.

The current GPIO initialization sets `COM_RTBC` High.

The required RT/BC logic level must be checked against the controller's operating-mode truth table before releasing reset.

---

### 3.2 Control and Handshake

| COM1553B Signal | STM32 Pin | User Label | GPIO Configuration | Status |
|:----------------|:----------|:-----------|:-------------------|:-------|
| CSTR | PD10 | `COM_CSTR` | Output Push Pull | Pending Validation |
| CSTRA | PD11 | `COM_CSTRA` | GPIO Input | Pending Validation |
| DTR | PB14 | `COM_DTR` | GPIO Input | Pending Validation |
| BGACK | PB15 | `COM_BGACK` | Output Push Pull | Pending Validation |
| DTACK | PC8 | `COM_DTACK` | Output Push Pull | Pending Validation |

#### 3.2.1 Control Output Configuration

| Signal | STM32 Pin | Initial Level | Pull Configuration | Output Speed |
|:-------|:----------|:--------------|:-------------------|:-------------|
| CSTR | PD10 | High | No Pull | Low |
| BGACK | PB15 | High | No Pull | Low |
| DTACK | PC8 | High | No Pull | Low |

All three control outputs use:

`GPIO_MODE_OUTPUT_PP`

The initial High levels correspond to the current CubeMX configuration.

The firmware must assert and release each signal according to the controller timing requirements.

#### 3.2.2 Handshake Input Configuration

| Signal | STM32 Pin | GPIO Mode | Pull Configuration |
|:-------|:----------|:----------|:-------------------|
| CSTRA | PD11 | Input | No Pull |
| DTR | PB14 | Input | No Pull |

The signals are initially monitored using standard GPIO inputs.

Potential interrupt-based handling will be evaluated during firmware development.

#### 3.2.3 Handshake Requirements

The firmware must support:

- Command strobe generation.
- Command strobe acknowledgment detection.
- Data transfer request detection.
- Bus grant acknowledgment.
- Data transfer acknowledgment.
- Parallel data bus direction management.

The precise ordering and timing must follow the COM1553B datasheet.

---

### 3.3 Parallel Data Bus

The 16-bit parallel data bus is distributed across GPIOE and GPIOD.

This arrangement provides an organized pin assignment while preserving PE9 for the reference clock.

#### 3.3.1 GPIOE — Data Bits D0–D7

| COM1553B Signal | STM32 Pin | User Label | Initial Mode | Status |
|:----------------|:----------|:-----------|:-------------|:-------|
| D0 | PE7 | `COM_D0` | GPIO Input | Pending Validation |
| D1 | PE8 | `COM_D1` | GPIO Input | Pending Validation |
| D2 | PE10 | `COM_D2` | GPIO Input | Pending Validation |
| D3 | PE11 | `COM_D3` | GPIO Input | Pending Validation |
| D4 | PE12 | `COM_D4` | GPIO Input | Pending Validation |
| D5 | PE13 | `COM_D5` | GPIO Input | Pending Validation |
| D6 | PE14 | `COM_D6` | GPIO Input | Pending Validation |
| D7 | PE15 | `COM_D7` | GPIO Input | Pending Validation |

#### 3.3.2 GPIOD — Data Bits D8–D15

| COM1553B Signal | STM32 Pin | User Label | Initial Mode | Status |
|:----------------|:----------|:-----------|:-------------|:-------|
| D8 | PD0 | `COM_D8` | GPIO Input | Pending Validation |
| D9 | PD1 | `COM_D9` | GPIO Input | Pending Validation |
| D10 | PD2 | `COM_D10` | GPIO Input | Pending Validation |
| D11 | PD3 | `COM_D11` | GPIO Input | Pending Validation |
| D12 | PD6 | `COM_D12` | GPIO Input | Pending Validation |
| D13 | PD7 | `COM_D13` | GPIO Input | Pending Validation |
| D14 | PD8 | `COM_D14` | GPIO Input | Pending Validation |
| D15 | PD9 | `COM_D15` | GPIO Input | Pending Validation |

#### 3.3.3 Data Bus Configuration

| Parameter | Configuration |
|:----------|:--------------|
| Data Width | 16 bits |
| GPIO Ports | GPIOE and GPIOD |
| Initial GPIO Mode | Input |
| GPIO Pull-up/Pull-down | No Pull |
| Direction Switching | Firmware controlled |
| Output Configuration | To be implemented |
| Bus Read Operation | Pending |
| Bus Write Operation | Pending |

The firmware will dynamically configure the GPIO direction according to the required transfer.

**Design Objective:**

The data bus implementation should support:

- Parallel data acquisition.
- Parallel data output.
- GPIO direction switching.
- Bit masking.
- Reconstruction of 16-bit data words.
- Bus release and contention prevention.

The GPIO distribution must be considered when implementing the data bus driver.

---

### 3.4 Status and Monitoring

| COM1553B Signal | STM32 Pin | User Label | GPIO Configuration | Status |
|:----------------|:----------|:-----------|:-------------------|:-------|
| R/W | PB13 | `COM_RW` | GPIO Input | Pending Validation |
| WE | PC9 | `COM_WE` | GPIO Input | Pending Validation |
| C/D | PC11 | `COM_CD` | GPIO Input | Pending Validation |
| TXMODE | PA3 | `COM_TXMODE` | GPIO Input | Pending Validation |
| MC | PA2 | `COM_MC` | GPIO EXTI2 | Pending Validation |
| IM | PC1 | `COM_IM` | GPIO EXTI1 | Pending Validation |
| MCF | PA1 | `COM_MCF` | GPIO Input | Pending Validation |
| VC | PC2 | `COM_VC` | GPIO Input | Pending Validation |

All standard input signals use:

```text
GPIO Mode: Input
GPIO Pull-up/Pull-down: No Pull
```

MC and IM are configured with external interrupt functionality.

---

## 4. External Interrupt Configuration

The STM32F407 uses external interrupt lines to detect communication events generated by the COM1553B.

### 4.1 Configured Interrupts

| Signal | STM32 Pin | EXTI Line | Trigger | NVIC |
|:-------|:----------|:----------|:--------|:-----|
| MC | PA2 | EXTI2 | Rising Edge | Enabled |
| IM | PC1 | EXTI1 | Falling Edge | Enabled |

Both interrupts currently use:

| Parameter | Value |
|:----------|:------|
| Preemption Priority | 0 |
| Subpriority | 0 |
| NVIC | Enabled |
| Pull-up/Pull-down | No Pull |

These configurations were generated by STM32CubeMX.

The selected trigger polarities must be validated against the actual COM1553B signal timing.

### 4.2 Interrupt Handling

The firmware should implement short interrupt service routines.

Interrupt handlers should avoid unnecessary blocking operations.

Communication events may be recorded using:

- Event flags.
- Status variables.
- Event counters.
- Error indicators.
- Communication state structures.

The main application or COM1553B communication module will process the recorded events.

### 4.3 Future Interrupt Evaluation

The following signals may require additional interrupt-driven handling:

- DTR
- CSTRA
- Other timing-critical communication events.

Their final handling strategy will depend on timing requirements and firmware architecture.

---

## 5. STM32 Clock Configuration

The STM32F407 system clock is configured through the PLL.

### 5.1 System Clock Parameters

| Parameter | Configuration |
|:----------|:--------------|
| HSE Input | 8 MHz |
| PLL Source | HSE |
| PLLM | 8 |
| PLLN | 336 |
| PLLP | 2 |
| PLLQ | 7 |
| SYSCLK | 168 MHz |
| HCLK | 168 MHz |
| APB1 Clock | 42 MHz |
| APB1 Timer Clock | 84 MHz |
| APB2 Clock | 84 MHz |
| APB2 Timer Clock | 168 MHz |

The system clock is calculated using:

```text
f_VCO = (8 MHz / 8) × 336

f_VCO = 336 MHz

f_SYSCLK = 336 MHz / 2

f_SYSCLK = 168 MHz
```

TIM1 belongs to the APB2 timer clock domain.

Its configured timer input clock is 168 MHz.

This configuration supports generation of the required 12 MHz reference clock.

### 5.2 Clock Validation

| Test | Expected Result | Status |
|:-----|:----------------|:-------|
| System Clock | 168 MHz | Configured |
| TIM1 Input Clock | 168 MHz | Configured |
| TIM1 PWM Frequency | 12 MHz | Pending Validation |
| PWM Duty Cycle | 50% | Pending Validation |
| Physical PE9 Output | Stable clock waveform | Pending |

---

## 6. Signal Configuration Requirements

### 6.1 GPIO Outputs

Control outputs must be initialized to defined states before normal communication begins.

The initialization sequence must avoid unintended:

- Controller resets.
- Control strobes.
- Transfer acknowledgments.
- Data bus conflicts.

Control signal assertion and release must follow the timing requirements of the COM1553B.

### 6.2 GPIO Inputs

Input signals are initially configured without internal pull-up or pull-down resistors.

The firmware must correctly interpret the status and handshake signals.

### 6.3 Bidirectional Data Bus

The firmware must explicitly manage the parallel data bus direction.

A general transfer sequence involves:

1. Determine the requested transfer direction.
2. Configure the STM32 data GPIOs.
3. Perform the required data read or write operation.
4. Generate or acknowledge the appropriate handshake.
5. Release or restore the data bus.

The exact sequence must follow the COM1553B timing requirements.

> [!WARNING]
> Bus direction switching must prevent simultaneous driving of the data lines.

### 6.4 GPIO Output Speed

Control GPIO outputs are initially configured with Low output speed.

The final settings must be validated against the timing requirements of each signal.

GPIO output speed controls electrical transition characteristics and does not directly define the frequency of a digital control signal.

### 6.5 Hardware Interface

External hardware connections, power distribution, and signal conditioning are handled separately by the hardware design.

This document focuses on STM32 pin assignments, GPIO configuration, signal direction, and firmware interaction.

---

## 7. Manchester Interface

The COM1553B uses a dedicated Manchester interface to communicate with the external MIL-STD-1553B transceiver.

| Signal | COM1553B Direction | Function |
|:-------|:-------------------|:---------|
| MANIN | Input | Manchester receive |
| Complementary MANIN | Input | Complementary receive |
| MANOUT | Output | Manchester transmit |
| Complementary MANOUT | Output | Complementary transmit |

These signals are not assigned to the STM32 GPIO interface.

The Manchester connections belong to the external transceiver hardware implementation.

---

## 8. Firmware Project Configuration

The firmware project was generated using STM32CubeMX and imported into STM32CubeIDE.

### 8.1 Project Information

| Parameter | Value |
|:----------|:------|
| Project Name | `1553_DISCO` |
| Target MCU | STM32F407VGT6 |
| Development Board | STM32F407G-DISC1 |
| Programming Language | C |
| Toolchain / IDE | STM32CubeIDE |
| Firmware Framework | STM32 HAL |
| Build Configuration | Debug |
| Build Result | Successful |
| Compiler Errors | 0 |
| Compiler Warnings | 0 |

### 8.2 Generated Firmware Components

The generated project includes:

- GPIO initialization.
- System clock configuration.
- TIM1 PWM configuration.
- EXTI1 interrupt configuration.
- EXTI2 interrupt configuration.
- STM32 HAL drivers.
- CMSIS support files.
- Startup code.
- Linker scripts.

Additional board-related peripherals and middleware were generated by CubeMX and may be removed if they are not required by the final application.

### 8.3 Firmware Build

The first firmware compilation completed successfully.

```text
Build Configuration: Debug

Build Finished

0 errors
0 warnings
```

The generated executable is:

```text
1553_DISCO.elf
```

The initial build reported:

| Section | Size |
|:--------|-----:|
| text | 39,408 bytes |
| data | 124 bytes |
| bss | 4,196 bytes |
| Total | 43,728 bytes |

The build result confirms successful compilation and linking.

It does not confirm physical communication with the COM1553B.

### 8.4 Firmware Development

The communication firmware will be implemented using separate software modules.

Planned modules include:

```text
com1553_config.h
com1553_gpio.c
com1553_gpio.h
com1553_handshake.c
com1553_handshake.h
com1553_control.c
com1553_control.h
com1553_status.c
com1553_status.h
com1553_rt.c
com1553_rt.h
```

These modules are planned components and are not part of the automatically generated CubeMX firmware.

---

## 9. Hardware Validation Plan

The following tests must be completed before assigning Verified status.

| Test | Description | Status |
|:-----|:------------|:-------|
| Clock Output | Measure 12 MHz PWM on PE9 | Pending |
| Controller Reset | Verify POR operation | Pending |
| RT Mode | Verify RT/BC selection | Pending |
| Data Bus Input | Validate D0–D15 input operation | Pending |
| Data Bus Output | Validate D0–D15 output operation | Pending |
| CSTR/CSTRA | Verify command strobe handshake | Pending |
| DTR/BGACK | Verify bus request and acknowledgment | Pending |
| DTACK | Verify transfer acknowledgment timing | Pending |
| Status Inputs | Validate status signal behavior | Pending |
| MC Interrupt | Verify message completion interrupt | Pending |
| IM Interrupt | Verify invalid message interrupt | Pending |
| RT Communication | Verify MIL-STD-1553B RT operation | Pending |

The firmware must not be considered operational until the required tests are completed successfully.

### 9.1 Initial Validation Sequence

The recommended initial validation sequence is:

1. Verify the STM32 firmware can be programmed and executed.
2. Start TIM1 PWM generation.
3. Measure the reference clock on PE9.
4. Validate controller reset operation.
5. Verify the RT/BC configuration.
6. Validate individual control GPIO outputs.
7. Validate handshake input signals.
8. Implement and test bidirectional bus operations.
9. Verify controller status and interrupt handling.
10. Perform functional Remote Terminal communication tests.

---

## 10. Physical Pin Accessibility

The STM32F407G-DISC1 provides external access to GPIO signals through its expansion connectors.

Pin assignments in this document refer to STM32 GPIO names.

The corresponding physical connector contacts must be confirmed using the schematic and user manual for the actual board revision.

The physical wiring documentation should include:

- STM32 GPIO name.
- Development board connector.
- Connector contact number.
- COM1553B signal name.
- COM1553B package pin number.
- Connection validation status.

Physical connector numbering remains subject to final board-level verification.

The STM32CubeMX `.ioc` file is the configuration reference for the assigned GPIO functions.

---

## 11. References

The following technical documents are used as references:

1. **COM1553B SMART Controller Datasheet** — Pin functions, control signals, handshake, and timing requirements.
2. **STM32F407 Reference Manual (RM0090)** — GPIO, timers, EXTI, NVIC, and clock configuration.
3. **STM32F407G-DISC1 User Manual (UM1472)** — Development board hardware and expansion interfaces.
4. **STM32F407G-DISC1 Schematics (MB997)** — Physical board connections and peripheral assignments.
5. **STM32CubeMX Documentation** — Pin configuration, clock configuration, and code generation.
6. **STM32CubeF4 HAL Documentation** — Hardware abstraction layer and peripheral drivers.

---

## 12. Revision History

| Revision | Description |
|:---------|:------------|
| 0.1 | Initial COM1553B signal classification, preliminary STM32 pinout, and clock configuration |
| 0.2 | Assigned STM32 GPIO pins, configured control and handshake signals, added EXTI settings, documented TIM1 clock configuration, and recorded successful initial firmware compilation |

---

**Project:** FIRMWARE_1553  
**Document:** Pinout  
**Operating Mode:** Remote Terminal (RT)  
**Status:** Configured — Pending Hardware Validation  
**Document Revision:** 0.2

