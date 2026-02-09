# 6 dB π Attenuation Pad

## Overview

This block implements a **fixed 6 dB π (pi) resistive attenuation pad** intended for use in a **50 ohm RF signal chain**
following the Si5351 output buffer and preceding the primary low-pass filter and gain stages.

The pad provides:

- Defined 50 ohm source and load impedance
- Controlled signal level into downstream filtering and amplification stages
- Additional suppression of residual edge energy and harmonics
- Improved stability and repeatability across bands and layouts

The design is intended for **HF operation from 160 m through 10 m (1.8 to 30 MHz)**.

---

## Intent and Design Goals

Resistive attenuation pads are used in this design to deliberately trade signal amplitude for:

- Impedance consistency
- Improved spectral behavior
- Reduced sensitivity to downstream loading
- Predictable drive conditions for filters and MMICs

This π pad is intended to be a **permanently installed, non-adjustable block** that establishes a known signal level and impedance environment for the rest of the RF chain.

---

## Circuit Description

The circuit is a **symmetric 50 ohm π attenuator** consisting of:

- One series resistor between input and output
- Two shunt resistors to ground at the input and output nodes
- An AC coupling capacitor at the input

### Functional sections

- **Input coupling capacitor (C1)**  
  Blocks DC while providing negligible reactance across the HF bands.

- **Series resistor (R2)**  
  Sets the primary attenuation and participates in impedance matching.

- **Shunt resistors (R1, R3)**  
  Define the input and output impedance and complete the π network.

---

## Component Selection Rationale

### Resistors

- **R2 = 37.4 ohm**
- **R1, R3 = 150 ohm**

These values implement approximately **6 dB of attenuation** while maintaining a **50 ohm match** at both ports.

Standard 1 percent tolerance resistors are recommended to preserve impedance accuracy.

### Capacitor

- **C1 = 10 nF**

At HF, this value presents negligible series reactance while remaining small, inexpensive, and layout-friendly.
NP0 or X7R dielectrics are acceptable.

---

## Performance Expectations

### Electrical Behavior

- Attenuation: approximately 6 dB
- Input impedance: 50 ohm nominal
- Output impedance: 50 ohm nominal
- Flat amplitude response across all HF bands

### Spectral Behavior

The pad provides:

- Additional reduction of high-order harmonics
- Reduced edge sensitivity prior to filtering
- Improved linearity margin for downstream MMIC stages

Although not frequency-selective, the pad improves the effectiveness of subsequent filtering stages.

---

## Integration Notes

### Placement in Signal Chain

Recommended placement:

```text
Si5351
    → RF Isolation & Edge Conditioning Buffer
    → 6 dB π Attenuation Pad
    → Primary Low-Pass Filter
    → MMIC Gain Block
    → Band-Specific Filtering / PA
```

Key points:

- The pad should be driven from a **low-impedance source**, such as the buffer stage.
- The pad should directly precede the **primary low-pass filter**.
- Additional attenuation may be added upstream if required for compression control.

### Optional 3 dB Configuration

If reduced attenuation is required, the pad may be reconfigured:

- Shunt resistors ≈ 292 ohm
- Series resistor ≈ 17.6 ohm

This option is intended as a design-time alternative, not a run-time adjustment.

---

## Layout Guidelines

- Keep resistor placement compact and symmetric.
- Provide solid ground connections for both shunt resistors.
- Place the coupling capacitor close to the input node.
- Avoid long stubs at the pad input and output.

---

## Summary

This 6 dB π attenuation pad establishes a predictable impedance and signal level early in the RF chain.
It improves spectral cleanliness, stabilizes downstream behavior, and simplifies gain planning.
The pad is intended to be permanently installed and requires no tuning.
