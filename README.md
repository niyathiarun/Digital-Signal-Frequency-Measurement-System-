# Digital Signal Frequency Measurement System (DSFMS)

A fully simulated Digital Signal Frequency Measurement System covering
hardware design, RTL implementation, firmware, logic analysis, and
automated verification — all running in a single interactive web page.

![DSFMS Screenshot](assets/screenshot.png)

---

## Live Demo

Open `simulation/dsfms.html` in any modern browser. No server needed.

---

## Project Overview

This project demonstrates a complete frequency measurement pipeline:
Signal Input → Schmitt Trigger → Prescaler → 32-bit Counter→
MCU/FPGA Compute Engine → UART Output + Display

The system measures how fast a digital signal switches on and off,
using three different measurement methods depending on the signal
characteristics — Gate Count, Reciprocal, and Period Measurement.

---

## Features

- Real-time oscilloscope waveform rendering (Canvas API)
- 4 signal types: Square, Sine→Square, PWM Burst, Glitched
- Frequency range: 1 Hz → 100 kHz (simulation) / 1 Hz → 100 MHz (hardware)
- 8-channel logic analyzer with live timing diagram
- Automated verification suite — 8 test cases with pass/warn/fail
- Synthesizable Verilog RTL for FPGA implementation
- STM32 HAL C firmware for microcontroller deployment
- UART output at 115200 baud

---

## System Specifications

| Parameter        | Value                        |
|------------------|------------------------------|
| Frequency Range  | 1 Hz – 100 MHz               |
| Resolution       | 0.1 Hz @ 1s gate             |
| Accuracy         | < 1 ppm (10 MHz TCXO)        |
| Counter Width    | 32-bit synchronous           |
| Prescaler        | ÷1, ÷2, ÷10, ÷100           |
| Gate Times       | 100ms / 1s / 10s             |
| Reference Clock  | 10 MHz TCXO ±1 ppm           |
| Input Voltage    | 0V – 5V TTL/CMOS             |
| Interface        | UART 115200 baud             |
| Display          | 6-digit 7-segment            |

---

## Tabs Explained

### Simulation
Live oscilloscope showing CH1 (input signal) and CH2 (gate signal).
Adjust frequency, duty cycle, noise, waveform type, gate time,
and prescaler in real time.

### Design
Block diagram of the full hardware architecture and system
specifications. Shows the reciprocal counting formula and
design rationale.

### Implementation
- **Verilog HDL** — Synthesizable RTL for the 32-bit frequency
  counter core. Targets any FPGA (tested on Xilinx/Intel families).
- **C Firmware** — STM32 HAL driver using TIM2 input capture
  and TIM3 gate timer with UART output.
- **State Machine** — 6-state measurement cycle from RESET to OUTPUT.

### Logic Analyzer
8-channel digital capture:

| Channel | Signal    | Description                  |
|---------|-----------|------------------------------|
| CH0     | SIG IN    | Raw input signal             |
| CH1     | SCHMITT   | Cleaned signal after trigger |
| CH2     | GATE      | Counting window              |
| CH3     | CLK÷1     | Reference clock              |
| CH4     | EDGE DET  | Rising edge detection pulse  |
| CH5     | COUNT_EN  | Counter enable               |
| CH6     | DATA_RDY  | Measurement ready strobe     |
| CH7     | UART TX   | Serial output bursts         |

### Verification
8 automated test cases with error analysis:

| Test Case      | Method         | Max Error |
|----------------|----------------|-----------|
| 1 Hz Square    | Gate Count     | 0.054%    |
| 100 Hz Square  | Gate Count     | 0.006%    |
| 1 kHz PWM      | Gate Count     | 0.019%    |
| 10 kHz Burst   | Reciprocal     | 0.002%    |
| 50 kHz Sine→Sq | Reciprocal     | 0.062%    |
| Glitch Filter  | Debounce       | 0.041%    |
| Duty Cycle 25% | Period Meas    | 0.014%    |
| Duty Cycle 75% | Period Meas    | 0.014%    |

---

## Measurement Methods

**Gate Count** — Opens a counting window for a fixed time,
counts edges, divides by gate duration. Best for 100 Hz – 10 kHz.

**Reciprocal** — Measures the time between two edges, computes
frequency as 1 ÷ period. Best for signals above 10 kHz.

**Debounce** — Filters false edges from noise before counting.
Used when signal integrity is poor.

**Period Measurement** — Measures both HIGH and LOW durations
to compute full period. Best for asymmetric duty cycles.

---

## File Structure

See below for full file structure and what goes in each file.
dsfms/
│
├── README.md                        ← The readme above
│
├── assets/
│   └── screenshot.png               ← Screenshot of the simulation
│
├── simulation/
│   └── dsfms.html                   ← THE MAIN FILE — entire simulation
│                                       (the HTML file already built)
│
├── rtl/
│   ├── freq_counter.v               ← Main Verilog module
│   ├── schmitt_trigger.v            ← Schmitt trigger module
│   ├── prescaler.v                  ← Prescaler ÷N module
│   └── gate_controller.v            ← Gate timebase generator
│
├── firmware/
│   ├── freq_measure.c               ← Main C firmware file
│   ├── freq_measure.h               ← Header file
│   └── uart_output.c                ← UART formatting and transmit
│
├── testbench/
│   ├── tb_freq_counter.v            ← Verilog testbench
│   └── sim_waveform.vcd             ← Simulation waveform output
│
└── docs/
    ├── system_architecture.png      ← Block diagram image
    ├── timing_diagram.png           ← Timing diagram image
    └── design_spec.pdf              ← Full design specification
    
---

## How to Run

```bash
# Clone the repo
git clone https://github.com/yourusername/dsfms.git

# Open simulation — no build needed
cd dsfms
open simulation/dsfms.html
```

---



## Author

**Niyathi Arun Kurthkoti**
Embedded Systems | Digital Design | FPGA | Firmware
[LinkedIn](www.linkedin.com/in/niyathiarun )•
[GitHub](https://github.com/yourusername)
