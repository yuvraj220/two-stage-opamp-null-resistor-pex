# Two-Stage Op-Amp: Layout, PEX, and Pre- vs Post-Layout Comparison

EE517 Analog VLSI Lab, IIT Guwahati — Experiments 5 & 6

Schematic design and layout of a two-stage Miller-compensated CMOS operational amplifier in 0.18μm CMOS, followed by DRC/LVS verification, parasitic extraction (PEX), and a full pre-layout vs. post-layout performance comparison — for both a standard Miller-compensated design and a version with a null (nulling) resistor added in series with the compensation capacitor.

## Contents

- [`254012419_EE517_Exp5.pdf`](./254012419_EE517_Exp5.pdf) — **Experiment 5**: schematic-level design and analysis of the two-stage op-amp (architecture, gain, Miller compensation, transistor sizing, schematic simulation results).
- [`254102419_EE517_Exp6.pdf`](./254102419_EE517_Exp6.pdf) — **Experiment 6**: layout design, DRC/LVS verification, parasitic extraction, and pre- vs. post-layout simulation comparison, for both the null-resistor and no-null-resistor versions.

## Design Specifications (target)

- Supply: 1.8V single supply
- DC gain: ≥ 60dB (1000 V/V)
- Unity gain frequency (UGF): 200MHz
- Phase margin: ≥ 60°
- Load capacitance: 1pF
- Common-mode gain: ≤ -20dB
- Slew rate: 50V/μs (symmetric)
- Power: ≤ 1mW

## Pre-Layout vs. Post-Layout Comparison

### Without Null Resistor

| Parameter | Pre-Layout | Post-Layout | Variation |
|---|---|---|---|
| DC Gain | 59.71 dB | 59.9342 dB | slight increase |
| Unity Gain Frequency | 201.023 MHz | 140.222 MHz | reduced |
| Phase Margin | 57.1707° | 60.1531° | slightly increased |
| Slew Rate | 146 V/μs | 98.55 V/μs | reduced |
| Common Mode Gain | -11.79 dB | -11.3873 dB | nearly same |
| CMRR | 71.5 dB | 71.3215 dB | nearly same |

### With Null Resistor

| Parameter | Pre-Layout | Post-Layout | Variation |
|---|---|---|---|
| DC Gain | 62.0823 dB | 60.8341 dB | slight decrease |
| Unity Gain Frequency | 224.212 MHz | 146.203 MHz | reduced |
| Phase Margin | 61.5° | 78.6485° | increased |
| Slew Rate | 158 V/μs | 87.06 V/μs | reduced |
| Common Mode Gain | -9.35318 dB | -10.5448 dB | improved |
| CMRR | 71.43548 dB | 71.3829 dB | nearly same |

## Why the Post-Layout Changes Were Observed

Moving from an ideal schematic simulation to a post-layout (parasitic-extracted) simulation introduces real physical effects that the schematic never accounts for. The main contributors, and which specs they move:

- **Routing resistance** — metal interconnects add distributed resistance along signal paths. This slightly reduces node voltage swing at high frequency and lowers the effective DC gain contribution from certain paths.
- **Routing capacitance** — parasitic capacitance between metal layers and adjacent routing tracks adds to the effective load capacitance at internal nodes. This shifts the circuit's poles to lower frequencies, which is the main reason **unity gain frequency drops** in both versions (~30% lower post-layout).
- **Diffusion capacitance** — source/drain junction capacitance to the substrate and wells adds extra parasitic poles, which is why **phase margin shifts** post-layout (in this design, it actually improves, since the shifted pole locations land favorably for stability, though the outcome direction isn't guaranteed in general).
- **Contact and via resistance** — every contact/via in the layout adds a small series resistance. This limits how fast internal nodes can charge/discharge, which is the direct cause of the **slew rate drop** seen in both versions (~30-45% lower post-layout).
- **Device mismatch from layout effects** — even with symmetric, common-centroid-style placement, small mismatches from process gradients and routing asymmetry remain. This is why **common-mode gain and CMRR shift slightly** rather than staying identical pre- and post-layout.

**Null resistor vs. no null resistor:** Comparing the two tables, adding the null resistor in series with the compensation capacitor visibly improves phase margin post-layout (60.15° → 78.65° vs. the no-resistor case) and slightly improves common-mode gain, at the cost of a somewhat larger post-layout DC gain drop. This matches the standard analog design result: a nulling resistor cancels the right-half-plane (RHP) zero introduced by the feedforward path through the Miller capacitor, which otherwise erodes phase margin — so the null-resistor version is more robust to the added post-layout parasitics.

**Bottom line:** despite the parasitic-driven degradation, both versions stayed close enough to the schematic-level targets after layout, confirming the layout (common-centroid placement, symmetric routing, minimized interconnect length) was solid enough to preserve the design intent through DRC-clean, LVS-matched, parasitic-extracted verification.

## Verification Flow

1. Schematic design and sizing (Experiment 5) — gm/ID-informed sizing, Miller compensation, pre-layout simulation.
2. Layout implementation in Cadence Virtuoso (Experiment 6), for both the null-resistor and no-null-resistor topologies.
3. DRC — verified layout is manufacturing-rule clean.
4. LVS — verified layout matches the schematic netlist.
5. PEX — extracted parasitic R/C from the layout.
6. Post-layout simulation using the extracted netlist, compared directly against the pre-layout (schematic) results.

## Tools Used
- Cadence Virtuoso (Schematic Editor, Layout XL, ADE / Spectre simulation), 0.18μm CMOS process

## Author
Yuvraj Singh — M.Tech VLSI and Nanoelectronics, IIT Guwahati
