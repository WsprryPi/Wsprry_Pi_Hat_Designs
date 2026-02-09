# Broadband MMIC Gain-Block Driver

## Overview

This block implements a **single-stage broadband MMIC gain block** used to raise the filtered RF signal
to a level suitable for downstream band-specific filtering and/or power amplification stages.

The design uses a **fixed-gain MMIC (e.g. GVA-84+)** operated well below compression to prioritize:

- Linearity and spectral cleanliness
- Predictable gain across the HF bands
- Stability with minimal external components

The block is intended for **HF operation from 160 m through 10 m (1.8 to 30 MHz)**. Gain varies with frequency.

---

## Intent and Design Goals

After attenuation and low-pass filtering, the RF signal level is deliberately conservative.
This MMIC stage provides controlled gain while maintaining adequate linearity margin.

The design goals are to:

- Restore signal level after attenuation and filtering
- Operate well below compression to minimize distortion
- Provide a stable, broadband gain stage without tuning
- Avoid reliance on bias trimming or adjustment

This block is not intended to provide maximum power output, but rather **clean, repeatable drive**
for later stages.

---

## Circuit Description

The circuit uses a **single MMIC gain block** with AC-coupled input and output,
and a supply feed designed to minimize noise and instability.

### Functional sections

- **Input coupling (C1)**  
  Blocks DC and passes HF with negligible attenuation.

- **Input damping resistor (R1)**  
  Provides additional stability and isolates the MMIC input from the preceding filter.

- **MMIC device (U1)**  
  Fixed-gain broadband gain block (e.g. GVA-84+).

- **Bias feed and isolation (L1, FB1)**  
  Prevents RF energy from coupling into the supply rail.

- **Supply decoupling (C2, C4, C5)**  
  Multi-value decoupling for wideband noise suppression.

- **Output coupling (C3)**  
  Blocks DC while delivering RF to the next stage.

---

## Component Selection Rationale

### MMIC

- **GVA-84+ (or equivalent)**
  - Fixed internal bias
  - Approximately 14–18 dB gain over HF (frequency dependent)
  - Good output power capability when operated linearly
  - Widely available and assembly-friendly

Equivalent gain blocks with similar biasing and gain characteristics may be substituted.

### Bias and isolation components

- **L1 = 10 µH**
  - Provides low-frequency DC feed with high RF impedance

- **Ferrite bead: 600 ohm @ 100 MHz**
  - Attenuates broadband supply noise

### Capacitors

- **C1, C3 = 1 µF**
  - AC coupling with negligible HF reactance

- **C2 = 0.1 µF, C4 = 1 µF, C5 = 10 µF**
  - Wideband local supply decoupling

### Resistor

- **R1 = 10 ohm**
  - Input damping and stability enhancement

---

## Performance Expectations

### Gain

- Gain is frequency dependent and varies across the HF bands.
- Typical small-signal gain is on the order of **14–18 dB**, depending on frequency.

### Output Power

- The stage is intended to operate well below compression.
- Clean output power is limited by MMIC linearity, not absolute gain.
- Typical usable output levels are suitable for driving band-pass filters or modest PA stages.

Exact performance depends on supply voltage, frequency, and device variant.

---

## Integration Notes

### Placement in Signal Chain

Recommended placement:

```text
Si5351
    → RF Isolation & Edge Conditioning Buffer
    → Fixed Attenuation Pad
    → Primary Low-Pass Filter
    → MMIC Gain Block
    → Band-Specific Filtering / PA
```

### Operating Point

- This stage assumes a **conservative drive level** from upstream blocks.
- No user adjustment or tuning is required.
- Additional attenuation should be added upstream if compression is observed.

### Power Supply

- Operates from a single supply rail.
- Local decoupling and isolation are mandatory.
- Do not share the MMIC supply feed directly with noisy digital loads.

---

## Layout Guidelines

- Place the MMIC close to the preceding filter output.
- Keep RF input and output traces short and symmetric.
- Stitch all ground pins directly into the ground plane with multiple vias.
- Place supply decoupling capacitors as close as possible to the bias pin.

Good layout is critical for stability and spectral performance.

---

## Testing and Validation

Recommended checks:

- Verify gain across HF bands.
- Monitor output spectrum to ensure operation below compression.
- Confirm stability into a 50 ohm load across frequency.

No tuning or adjustment is required under normal operation.

---

## Summary

This broadband MMIC gain-block driver provides clean, predictable gain following the primary low-pass filter.
It restores signal level while preserving spectral integrity and avoids the need for tuning or complex biasing.
The stage is intended to be used conservatively and as part of a fixed RF gain plan.
