# Digital Clock & Stopwatch — 74-Series TTL Logic

**Course:** Digital Systems EE-272 | B.S. Electrical Engineering, 4th Semester  
**Institution:** UET Lahore New Campus  
**Student ID:** 2024-EE-264

---

## Overview

A 24-hour digital clock and functional stopwatch built entirely from discrete **74-series TTL ICs** — no microcontroller, no firmware. Every second of timing, every digit on the display, and every control function is handled purely by hardware logic.

The project covers the full hardware development cycle: circuit design → Proteus simulation → PCB layout in EasyEDA → fabrication.

---

## PCB Photos

| Front | Back |
|---|---|
| ![PCB Front](media/pcb_front.jpg) | ![PCB Back](media/pcb_back.jpg) |

---

## Features

- 24-hour clock display (HH:MM:SS) across 6 × 7-segment displays
- Stopwatch mode with **Start**, **Stop**, and **Reset** push-button control
- 1Hz clock source from NE555 timer in astable configuration
- BCD counting via cascaded 74LS90 decade counters (Mod-10 and Mod-6)
- 7-segment output decoded by 74LS47
- Pure TTL discrete logic — no microcontroller

---

## Components

| Component | Part | Purpose |
|---|---|---|
| Timer | NE555 | 1Hz astable clock generator |
| Counter | 74LS90 | Decade counter (Mod-10 and Mod-6 configs) |
| Decoder | 74LS47 | BCD to 7-segment decoder |
| Display | 7-Segment (Common Anode) | Time display |
| Gates | 74LS08 (AND) | Reset detection and clock gating |
| Gates | 74LS32 (OR) | Control logic |
| Gates | 74LS04 (NOT) | Signal inversion |
| Passive | Resistors (330Ω), Capacitors | Current limiting, timing, decoupling |
| Power | 5V DC regulated | Supply |

---

## Circuit Design

### 4.1 Clock Generation — NE555 Astable

The NE555 is configured in astable mode to produce a continuous 1Hz square wave:

```
f = 1.44 / ((R1 + 2×R2) × C)
```

Component values: **R1 = 1kΩ, R2 = 72kΩ, C = 10µF** → f ≈ 1Hz (1 pulse/second)

---

### 4.2 Seconds & Minutes — Mod-60 Counting

Each section (seconds, minutes) uses **two cascaded 74LS90 ICs**:

**Units digit (Mod-10):**
- Clock pulse feeds into `CPA`; `QA` tied to `CPB` for standard BCD counting
- Counts 0–9 naturally; `QD` falling edge triggers the tens stage

**Tens digit (Mod-6):**
- Time rolls over at 59, not 99
- Reset at count 6 (binary `0110`) by wiring `QB` and `QC` into Master Reset pins `R0(1)` and `R0(2)`

---

### 4.3 Hours — Mod-24 Counting

For a 24-hour clock, the system resets to `00:00:00` upon reaching 24:

- Units digit: Mod-10 counter
- Tens digit: Mod-3 counter
- An AND gate monitors output `"2"` on the tens counter and `"4"` on the units counter
- When both are HIGH simultaneously, the AND gate output triggers Master Reset on both hour counters

---

### 4.4 Stopwatch Control Logic

**Start/Stop:**
- An AND gate is placed between the 555 timer output and the first counter's clock input
- A toggle switch / SR flip-flop controls the second leg of the AND gate, gating the 1Hz pulse on or off

**Reset:**
- A push-button connects directly to the Master Reset pins of all 74LS90 ICs in the stopwatch path
- Asynchronously clears all displays to `00` on press

---

## Simulation

All logic was verified in **Proteus** before PCB fabrication:

- Mod-6 reset condition confirmed (resets at binary `0110`)
- Mod-24 reset condition confirmed (AND gate fires at hours = `24`)
- 74LS90 cascading and `QD` carry triggers validated
- Stopwatch AND-gate gating logic tested
- Logic probes used to monitor pin states throughout

Simulation file: [`/simulation/digital_clock.pdsprj`](simulation/)

---

## PCB Design

Schematic captured and PCB laid out in **EasyEDA**. Gerber files exported and sent for fabrication.

- Schematic: [`/schematic/`](schematic/)
- Gerber files: [`/pcb/gerber/`](pcb/gerber/)

---

## Challenges & Solutions

| Challenge | Solution |
|---|---|
| Switch bounce causing multiple counts per press | Added debouncing capacitors across push buttons |
| Floating TTL inputs causing erratic reset behavior | All unused reset pins explicitly tied to GND |
| Mod-6 tens counter resetting incorrectly | Verified QB+QC wiring to R0(1) and R0(2) in Proteus before hardware |

---

## Key Concepts Demonstrated

- Asynchronous cascading counters
- Modulo-N counter design via hardware feedback reset
- BCD to 7-segment decoding
- Clock gating for stopwatch control
- TTL floating input handling
- RC debouncing for mechanical switches
- Full design-to-fabrication workflow (EasyEDA → Gerber → PCB)

---

## Repository Structure

```
digital-clock-stopwatch-pcb/
├── README.md
├── report/
│   └── Digital_Clock_Stopwatch_Report.pdf
├── schematic/
│   └── schematic.pdf
├── simulation/
│   └── digital_clock.pdsprj
├── pcb/
│   └── gerber/
│       └── (Gerber files)
└── media/
    ├── pcb_front.jpg
    └── pcb_back.jpg
```

---

## Author

**Muhammad Shehroz Akhter**  
B.S. Electrical Engineering — Computer Engineering Track  
UET Lahore New Campus (Class of 2028)  
[GitHub](https://github.com/EngrShehrozAkhterCh)
