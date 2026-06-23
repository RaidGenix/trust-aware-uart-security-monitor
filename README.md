# Trust-Aware FPGA UART Communication System

A hardware **UART security monitor** built on a Basys-3 FPGA that performs real-time packet inspection, CRC-16 error checking, and replay-attack detection, with adaptive trust-based response logic.

![Verilog](https://img.shields.io/badge/HDL-Verilog-blue)
![FPGA](https://img.shields.io/badge/FPGA-Artix--7%20%2F%20Basys--3-orange)
![License](https://img.shields.io/badge/license-MIT-green)
![Status](https://img.shields.io/badge/status-active-brightgreen)

---

## 📌 Overview

This project implements a **trust-aware UART communication security system** in Verilog, targeting the Basys-3 (Artix-7) FPGA. It inspects incoming UART packets in real time, validates their integrity using CRC-16-CCITT, detects replay attacks via sequence-gap analysis, and dynamically adjusts a communication "trust level" — switching between **ALLOW**, **THROTTLE**, and **BLOCK** modes based on detected anomaly severity.

### Key Features
- ✅ Real-time UART **packet parser** and inspection pipeline
- ✅ **CRC-16-CCITT** validator for data integrity checking
- ✅ **Replay-attack detection** via sequence-gap checking
- ✅ **Burst anomaly detector** for abnormal traffic patterns
- ✅ **Adaptive trust logic** with 3 operating modes: `ALLOW`, `THROTTLE`, `BLOCK`
- ✅ Fully modular, independently synthesizable submodules
- ✅ Timing closure achieved at **100 MHz**
- ✅ Extremely lightweight: **<1% LUT/FF utilization** on Artix-7

---

## 🏗️ Architecture

```
   UART RX ──▶ Packet Parser ──▶ CRC-16 Validator ──▶ Anomaly Detector
                                                              │
                                                              ▼
                                                    Sequence-Gap Checker
                                                              │
                                                              ▼
                                                  Adaptive Trust Controller
                                                   (ALLOW / THROTTLE / BLOCK)
                                                              │
                                                              ▼
                                                        UART TX / Output
```

### Trust Mode State Machine
```
        ┌────────┐   anomaly detected    ┌──────────┐   severe/repeated   ┌───────┐
        │ ALLOW  │ ─────────────────────▶│ THROTTLE │ ────────────────────▶│ BLOCK │
        └────────┘                       └──────────┘                     └───────┘
             ▲                                 │                               │
             └─────────────── trust recovered ─┴──────── manual reset ─────────┘
```

| Mode | Behavior |
|------|----------|
| **ALLOW** | Normal operation, all valid packets pass through |
| **THROTTLE** | Suspicious activity detected — rate-limits packet processing |
| **BLOCK** | Severe anomaly / repeated violation — communication halted |

---

## 📂 Repository Structure

```
uart-trust-security/
├── rtl/
│   ├── uart/
│   │   ├── uart_rx.v
│   │   └── uart_tx.v
│   ├── packet/
│   │   └── packet_parser.v
│   ├── integrity/
│   │   └── crc16_ccitt.v
│   ├── security/
│   │   ├── burst_anomaly_detector.v
│   │   ├── sequence_gap_checker.v
│   │   └── trust_controller.v
│   └── top/
│       └── uart_trust_top.v
├── tb/
│   ├── tb_uart_trust_top.v
│   ├── replay_attack_tests/
│   └── crc_fault_injection_tests/
├── constraints/
│   └── basys3_uart.xdc
├── docs/
│   ├── trust_fsm.md
│   └── resource_utilization_report.txt
├── scripts/
│   └── run_synth.tcl
├── LICENSE
└── README.md
```

---

## 🛠️ Tools & Technologies

| Category | Tools |
|----------|-------|
| HDL | Verilog |
| Target Hardware | Basys-3 FPGA (Artix-7) |
| Protocol | UART |
| Synthesis | Xilinx Vivado |
| Security Techniques | CRC-16-CCITT, sequence-gap replay detection, anomaly scoring |

---

## 🚀 Getting Started

### Prerequisites
- Vivado 2020.2+
- Basys-3 FPGA board
- USB-UART connection / serial terminal (e.g., PuTTY, TeraTerm)

### Simulation
```bash
cd sim
vivado -mode batch -source ../scripts/run_sim.tcl
```

### FPGA Deployment
```bash
vivado -mode batch -source scripts/run_synth.tcl
# Flash bitstream to Basys-3, connect via UART terminal
```

---

## ✅ Verification

- **CRC fault injection tests** — corrupted packets correctly flagged and rejected
- **Replay attack simulation** — out-of-sequence/replayed packets detected via gap checker
- **Burst anomaly tests** — abnormal traffic bursts correctly trigger THROTTLE/BLOCK transitions
- **Timing closure** verified at 100 MHz in Vivado implementation reports

---

## 📊 Results

| Metric | Result |
|--------|--------|
| Max clock frequency | 100 MHz (timing closure achieved) |
| Resource utilization | <1% LUT/FF on Artix-7 |
| Detection capability | CRC errors, replay attacks, burst anomalies |
| Trust modes | ALLOW, THROTTLE, BLOCK |

---

## 🔭 Future Work
- Add encrypted UART payload support (AES-lite)
- Configurable trust thresholds via register interface
- Logging subsystem for security event history
- Extend to SPI/I2C protocol monitoring

---

## 📄 License
This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

## ✍️ Author
**Kartik Sharma**
B.Tech ECE, IIIT Nagpur
[LinkedIn](https://linkedin.com/in/kartik-sharma-59884129a) • [GitHub](https://github.com/RaidGenix)

> 🏆 Related work: 1st place, Dark Silicon (VLSI design) event — Tantrafesta 2025, IIIT Nagpur (Team R432).
