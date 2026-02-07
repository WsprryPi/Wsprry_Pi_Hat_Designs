# HF RF Chain – Test Plan and Expected Power Levels

## Purpose

This document defines the **recommended test plan**, **measurement locations**, and **expected RF power levels**
for the HF RF chain described in the top-level *HF RF Chain Architecture and Gain Plan* README.

The intent is to:

- Validate each block independently during bring-up
- Confirm correct impedance and stability assumptions
- Detect compression or spectral issues early
- Set realistic expectations for final output power by HF band

This document covers **RF blocks only** and assumes band-specific filtering or a PA follows the MMIC.

---

## RF Chain

- 27 MHz TCXO Reference →
- Si5351 Clock Generator →
- RF Isolation & Edge Conditioning Buffer →
- 6 dB π Attenuation Pad →
- Primary Low-Pass Filter (≤ 28 MHz) →
- Broadband MMIC Gain Block →

All RF interfaces after the buffer are assumed to be **50 ohms**.

---

## Recommended Test Equipment

Minimum practical setup:

- Spectrum analyzer (to at least 200 MHz)
- Fixed 50 ohm attenuators (6 dB / 10 dB / 20 dB)
- 50 ohm dummy load (≥ 0.5 W)
- DC block (if analyzer input is not DC protected)
- Short, known-good coax cables

Optional but useful:

- Calibrated RF power meter
- Oscilloscope (for sanity checks, not power measurement)

---

## Test Points

Use logical test points rather than probing mid-trace:

| Label | Location | Notes |
| --- | --- | --- |
| TP0 | Si5351 CLK0 output | Digital source, relative reference only |
| TP1 | Buffer output | First RF-like node |
| TP2 | 6 dB pad output | First true 50 ohm node |
| TP3 | Primary LPF output | Spectrally clean HF node |
| TP4 | MMIC output | Highest power point in this chain |

Measurements at TP2 and beyond should always be made into **50 ohms**.

---

## Bring-Up and Test Sequence

### 1. Reference and Frequency Validation

- Verify TCXO oscillation and frequency.
- Confirm Si5351 is configured for external reference.
- Confirm output frequency on a mid-band frequency (e.g., 14.0971 MHz).

Acceptance:

- Frequency accuracy consistent with TCXO specification.
- No observable drift related to Pi activity.

---

### 2. Si5351 Output (TP0)

Purpose:

- Confirm correct configuration.
- Confirm signal presence.

Notes:

- TP0 is not a 50 ohm source.
- Use spectrum analyzer for **relative** checks only.

Acceptance:

- Strong fundamental.
- Harmonics expected and ignored at this stage.

---

### 3. Buffer Stage (TP1)

Purpose:

- Verify isolation and stability.

Checks:

- Measure TP1 into a 50 ohm analyzer input.
- Move cable, change analyzer attenuation.

Acceptance:

- Signal level remains stable.
- No oscillation or ringing.
- No sensitivity to load changes.

---

### 4. 6 dB π Pad (TP2)

Purpose:

- Establish known impedance and level.

Checks:

- Measure level difference between TP1 and TP2.

Acceptance:

- Approximately 6 dB reduction.
- Repeatable reading across reconnects.

This is the **first meaningful RF power reference point**.

---

### 5. Primary LPF (TP3)

Purpose:

- Verify harmonic suppression before gain.

Checks:

- Measure insertion loss across HF bands.
- Compare harmonic levels at TP2 vs TP3.

Acceptance:

- < 0.5 dB insertion loss in passband.
- Significant harmonic reduction.

---

### 6. MMIC Gain Block (TP4)

Purpose:

- Restore RF level without compression.

Checks:

- Measure output with ≥ 10 dB external attenuation.
- Sweep frequency from 160 m to 10 m.
- Check harmonic growth vs input level.

Acceptance:

- Gain roughly consistent with MMIC expectations.
- Output changes proportionally with input.
- No flattening or harmonic explosion.

---

## Expected Power Levels (Planning Targets)

The following are **typical planning ranges**, not guarantees.

Assumptions:

- Conservative Si5351 drive setting
- Buffer operating at unity voltage gain
- 6 dB pad installed
- LPF insertion loss ≈ 0.2–0.6 dB
- MMIC operated below compression

### Stage-by-Stage (Fundamental Only)

| Test Point | Expected Level |
| --- | --- |
| TP0 | Not meaningful in dBm |
| TP1 | Similar voltage to TP0 |
| TP2 | ~ -4 dBm to 0 dBm |
| TP3 | ~ -4.5 dBm to -0.5 dBm |
| TP4 | ~ +9.5 dBm to +15 dBm |

---

## Expected MMIC Output by HF Band

MMIC gain is frequency dependent. Expect slightly lower output near 10 m.

| Band | Frequency (MHz) | Expected TP4 Output |
| --- | ---: | ---: |
| 160 m | 1.8 | +12 to +15 dBm |
| 80 m | 3.5 | +12 to +15 dBm |
| 40 m | 7.0 | +11 to +15 dBm |
| 30 m | 10.1 | +11 to +14.5 dBm |
| 20 m | 14.1 | +11 to +14 dBm |
| 17 m | 18.1 | +10.5 to +13.5 dBm |
| 15 m | 21.1 | +10 to +13 dBm |
| 12 m | 24.9 | +10 to +12.5 dBm |
| 10 m | 28.1 | +9.5 to +12 dBm |

If outputs are consistently above these ranges, verify compression and consider additional fixed attenuation.

---

## Compression Screening

Simple field test:

- Increase Si5351 drive by one step.
- Measure TP4 delta.

Expected:

- Linear region: TP4 changes proportionally.
- Compression: TP4 increases less than expected and harmonics rise.

---

## Spectral Acceptance Guidance

Before band-pass filtering:

- Fundamental dominates spectrum.
- Harmonics visible but controlled.
- No unexplained spurs.

Final emissions compliance is determined by the band-specific filter or PA that follows.

---

## Summary

This test plan validates the HF RF chain incrementally and sets realistic expectations for output power.
The architecture favors fixed gain, linear operation, and repeatability across all HF bands.
