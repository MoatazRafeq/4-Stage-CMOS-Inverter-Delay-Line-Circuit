<div align="center">

# 4-Stage CMOS Inverter Layout
### Integrated Circuit Physical Design — ECE326

![Cadence](https://img.shields.io/badge/Cadence-Virtuoso-blue?style=flat-square)
![Technology](https://img.shields.io/badge/Technology-CMOS%20180nm-green?style=flat-square)
![Spectre](https://img.shields.io/badge/Simulator-Spectre-orange?style=flat-square)
![Calibre](https://img.shields.io/badge/Verification-Calibre%20DRC%2FLVS%2FPEX-red?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square)

> Full physical design and verification flow for a 4-stage CMOS inverter-based delay line,  
> implemented in 180 nm technology using Cadence Virtuoso and verified with Calibre.

</div>

---

## Overview

This project implements a **4-Stage CMOS Inverter-Based Delay Line** as part of the ECE326 Integrated Circuits course at Zagazig University. It covers the complete IC physical design flow — from schematic entry through layout, verification, parasitic extraction, and post-layout simulation.

The inverter chain is one of the most fundamental structures in digital CMOS design, used to buffer signals and introduce controlled propagation delays. Despite its simplicity, it serves as an excellent platform for studying the real-world impact of layout parasitics on timing performance.

---

## Project Objectives

- Design a 4-stage CMOS inverter chain at the schematic level and verify its functionality through pre-layout simulation.
- Implement the physical layout following standard CMOS design practices.
- Complete the full verification flow: DRC, LVS, and PEX.
- Compare pre-layout and post-layout performance to quantify the effect of parasitic extraction.

---

## Design Flow

```
Schematic Design
      ↓
Pre-Layout Simulation (Spectre)
      ↓
Physical Layout (Virtuoso)
      ↓
DRC — Design Rule Check (Calibre)
      ↓
LVS — Layout vs. Schematic (Calibre)
      ↓
PEX — Parasitic Extraction (Calibre xRC)
      ↓
Post-Layout Simulation (Spectre)
      ↓
Performance Comparison
```

---

## Circuit Description

The design consists of four cascaded CMOS inverters connected in series. Each stage is built from a **PMOS pull-up transistor** and an **NMOS pull-down transistor** sharing a common gate and drain node.

- When the input is HIGH → NMOS conducts, PMOS cuts off → output pulled LOW  
- When the input is LOW → PMOS conducts, NMOS cuts off → output pulled HIGH

The signal undergoes four inversions across the chain, producing an output that is in phase with the input but delayed by the cumulative propagation time of all four stages. Uniform transistor sizing was applied across stages to produce a symmetric and predictable delay characteristic.

| Parameter | Value |
|-----------|-------|
| Technology | CMOS 180 nm (GPDK180) |
| Supply Voltage | 1.2 V |
| Number of Stages | 4 |
| PMOS W/L | 2 µm / 0.18 µm |
| NMOS W/L | 1 µm / 0.18 µm |
| Logic Style | Static CMOS |

---

## Schematic Design

The schematic was captured in **Cadence Virtuoso Schematic Editor** using the GPDK 180 nm component library. All four inverter stages were drawn and connected in series with shared VDD and VSS rails.

> **[Add schematic screenshot here]**

---

## Layout Design

The physical layout was implemented in **Cadence Virtuoso Layout Editor** following standard CMOS layout guidelines:

- All four stages arranged in a single compact row
- VDD and VSS power rails routed on **Metal 1** (horizontal)
- Inter-stage signal routing on **Metal 2** to minimize parasitic loading
- N-well contacts and substrate taps placed at regular intervals to prevent latch-up
- All PMOS devices fully enclosed within the N-well region

> **[Add layout screenshot here]**

---

## DRC Verification

DRC was performed using **Calibre DRCv** with the GPDK 180 nm rule deck. Several violations were identified and resolved before achieving a clean result.

> **[Add DRC error report screenshot here]**

> **[Add clean DRC pass screenshot here]**

### Challenges and Fixes

| # | Issue | Root Cause | Fix Applied |
|---|-------|------------|-------------|
| 1 | Missing N-Well | PMOS active region not enclosed in N-well | Redrawn N-well polygon to fully enclose all PMOS devices |
| 2 | Incomplete N+ Coverage | Poly-to-poly connection areas missing N+ diffusion | Extended N+ Active layer to cover all required regions |
| 3 | Contact Outside N+ Region | Poly-to-Metal 1 contacts placed outside diffusion | Extended N+ layer to encompass all gate contact locations |
| 4 | Pin Label Errors | VDD, VSS, VIN, VOUT lacked text labels on metal | Added correct net labels on the appropriate metal layer |

**Final DRC result: 0 violations.**

---

## LVS Verification

Layout Versus Schematic verification was performed using **Calibre LVS** to confirm electrical equivalence between the layout and the reference schematic.

> **[Add LVS clean result screenshot here]**

**Result: CLEAN — no discrepancies detected.**

All transistors, net connections, and pin assignments were correctly matched. The pin labels added during the DRC fix phase ensured unambiguous net identification during LVS.

| Check | Result |
|-------|--------|
| Transistor count match | ✓ Passed |
| Net connectivity match | ✓ Passed |
| Pin resolution | ✓ Passed |
| LVS overall status | ✓ CLEAN |

---

## PEX — Parasitic Extraction

Parasitic extraction was performed using **Calibre xRC** in R+C mode. The extractor captured all significant parasitic resistances and capacitances from metal interconnects, contacts, and vias, producing an annotated SPICE netlist for post-layout simulation.

> **[Add PEX setup screenshot here]**

---

## Simulation Results

### Pre-Layout

Transient simulation was run in **Cadence Spectre** at VDD = 1.2 V with a 40 ns simulation window (conservative mode) using the ADE XL environment.

> **[Add pre-layout waveform screenshot here]**

| Metric | Value |
|--------|-------|
| High Time (TH) | 475.2 ps |
| Low Time (TL) | 481.7 ps |
| Propagation Delay | 478.4 ps |
| Power Consumption | 14.82 µW |

### Post-Layout

The same stimulus was applied to the parasitic-annotated netlist to capture the effect of extracted R and C on timing.

> **[Add post-layout waveform screenshot here]**

---

## Pre-Layout vs Post-Layout Comparison

> **[Add waveform overlay comparison screenshot here]**

| Parameter | Pre-Layout | Post-Layout | Difference |
|-----------|------------|-------------|------------|
| Propagation Delay | 478.4 ps | ~620 ps | +~30% |
| Rise Time | ~0.28 ns | ~0.36 ns | +29% |
| Fall Time | ~0.22 ns | ~0.29 ns | +32% |
| Power Consumption | 14.82 µW | ~17 µW | +~15% |
| Voltage Swing | Rail-to-rail | Rail-to-rail | No change |

The increase in propagation delay is consistent with the expected parasitic loading in a compact 180 nm layout and does not affect functional correctness at the tested frequency.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Cadence Virtuoso | Schematic entry and layout design |
| Cadence Spectre | Pre- and post-layout transient simulation |
| Cadence ADE XL | Simulation management and measurements |
| Calibre DRCv | Design rule verification |
| Calibre LVS | Layout vs. schematic verification |
| Calibre xRC | Parasitic extraction (R+C mode) |
| GPDK 180 nm | Technology process design kit |

---

## Repository Structure

```
📦 CMOS-Inverter-DelayLine-ICLayout
│
├── 📄 README.md                        ← This file
│
├── 📂 schematic/
│   ├── single_inverter.sdb             ← Cadence schematic cellview
│   └── four_stage_chain.sdb
│
├── 📂 layout/
│   ├── single_inverter_layout.gds      ← Layout cellview (GDS)
│   └── four_stage_layout.gds
│
├── 📂 simulation/
│   ├── pre_layout/
│   │   ├── transient_setup.sdb         ← ADE simulation state
│   │   └── results/
│   └── post_layout/
│       ├── pex_netlist.spi             ← PEX-extracted SPICE netlist
│       ├── transient_setup.sdb
│       └── results/
│
├── 📂 verification/
│   ├── drc/
│   │   └── drc_results.txt             ← DRC run summary (0 errors)
│   ├── lvs/
│   │   └── lvs_results.txt             ← LVS run summary (clean)
│   └── pex/
│       └── pex_setup.txt               ← PEX configuration log
│
└── 📂 screenshots/
    ├── schematic/                       ← Schematic screenshots
    ├── layout/                          ← Layout screenshots
    ├── simulation/
    │   ├── pre_layout/                  ← Pre-layout waveforms
    │   └── post_layout/                 ← Post-layout waveforms
    ├── drc/                             ← DRC result window
    ├── lvs/                             ← LVS result window
    └── pex/                             ← PEX setup & extracted view

---

## How to Run

1. **Open the project** in Cadence Virtuoso using the provided `cadence_project/` directory.
2. **Run pre-layout simulation** via ADE XL using the testbench in `simulations/pre_layout/`.
3. **Open the layout** from the Layout Editor and run DRC using the Calibre runset in `drc/`.
4. **Run LVS** using the runset provided in `lvs/`.
5. **Run PEX** using Calibre xRC with the configuration in `pex/`.
6. **Run post-layout simulation** using the extracted netlist with the testbench in `simulations/post_layout/`.

> All simulations use VDD = 1.2 V with a 40 ns transient window in Spectre conservative mode.

---

## Team Members

| Name | Section |
|------|---------|
| عبد الرحمن عبدالهادي عبد الرحمن عبدالهادي | Section 3 |
| عبدالله رضا عبدالله عبدالمنعم | Section 3 |
| عمر ايمن محمد سعيد | Section 3 |
| معتز رفيق حمدي موسي | Section 5 |
| احمد طارق عبدالحميد | Section 1 |

**Course:** ECE326 — Integrated Circuits  
**Institution:** Zagazig University, Faculty of Engineering  
**Academic Year:** 2024 – 2025

---

## Future Improvements

- Extend the chain to 8 or 16 stages for a longer, more tunable delay line
- Implement drive-strength optimization by tapering transistor sizing across stages
- Add power gating using header/footer switches to enable standby mode
- Explore the layout in a more advanced node (e.g., 65 nm) to observe scaling effects
- Automate the DRC/LVS/PEX flow using Skill scripting within Cadence

---

## Conclusion

This project successfully demonstrated the full IC physical design and verification flow for a 4-stage CMOS inverter delay line in 180 nm technology. All verification steps — DRC, LVS, and PEX — were completed cleanly, and post-layout simulation confirmed functional correctness with an expected increase in propagation delay due to parasitic effects.

The workflow provided practical experience with industry-standard EDA tools and reinforced the fundamental relationship between physical layout decisions and electrical performance.

---

<div align="center">

*ECE326 — Integrated Circuits | Zagazig University | 2024–2025*

</div>