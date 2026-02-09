# 27 MHz TCXO Reference Circuit

## Overview

This block implements a **low-noise 27 MHz temperature-compensated crystal oscillator (TCXO) reference**
used as the master frequency source for the Si5351.

The circuit provides:

- A stable, low phase-noise reference clock
- Isolation from digital and switching supply noise
- Controlled signal delivery into the Si5351 XA input

The design is intended for **HF weak-signal applications**, where reference stability and phase noise
directly affect transmitted spectral purity.

---

## Intent and Design Goals

The reference oscillator defines the absolute frequency accuracy and contributes directly to
the phase noise of all downstream RF generation.

This circuit is designed to:

- Minimize supply-coupled phase noise
- Isolate the TCXO from digital rail noise
- Deliver a clean, well-damped reference signal to the Si5351
- Avoid the need for trimming or tuning

The block is intentionally simple and passive beyond the TCXO device itself.

---

## Circuit Description

The circuit consists of a **standalone TCXO module** powered from an isolated supply island,
with AC-coupled output delivery to the Si5351 reference input.

### Functional sections

- **Supply isolation (FB1)**  
  A ferrite bead isolates the TCXO supply island from the main 3.3 V rail.

- **Local decoupling (C1, C2)**  
  Broadband decoupling suppresses supply noise close to the TCXO.

- **Output damping (R1)**  
  Limits edge rate and isolates the TCXO output from downstream loading.

- **AC coupling (C3)**  
  Blocks DC between the TCXO and the Si5351 reference input.

- **Optional series termination (R2)**  
  Provides additional damping if the reference trace is long.

- **Test points (TP1–TP2)**  
  Allow probing of reference signal and ground during bring-up and validation.

---

## Component Selection Rationale

### TCXO

- **27.000 MHz TCXO**
- CMOS output
- Nominal 3.3 V supply
- Temperature-compensated for improved stability over ambient variation

The exact TCXO part may be substituted, provided output level and supply requirements are compatible.

### Supply components

- **Ferrite bead: 600 ohm @ 100 MHz**
  - Attenuates digital and regulator noise from the main rail

- **C1 = 0.1 µF, C2 = 1.0 µF**
  - Wideband local decoupling directly at the TCXO

### Output network

- **R1 = 33 ohm**
  - Output damping and isolation

- **C3 = 1 nF**
  - AC coupling with negligible HF attenuation

- **R2 = 0 ohm (default)**
  - Stuff 22 ohm if trace length exceeds approximately 25–50 mm

---

## Performance Expectations

- Frequency accuracy determined by TCXO specification
- Reduced phase noise compared to crystal-resonator reference modes
- Insensitive to digital activity elsewhere on the board

The TCXO reference improves frequency stability and reduces phase noise modulation
in the generated RF outputs.

---

## Integration Notes

### Placement and Layout

- Place the TCXO and decoupling capacitors as a tight local cluster.
- Keep the isolated supply island compact.
- Connect grounds directly to the solid ground plane.
- Keep the reference trace short and direct into the Si5351 XA pin.

### Signal Chain Context

```text
TCXO (27 MHz)
    → AC-Coupled Reference Path
    → Si5351 Reference Input (XA)
    → RF Generation Chain
```

### Optional Damping

- Leave **R2** unpopulated or stuffed as 0 ohm by default.
- Populate R2 with approximately 22 ohm only if ringing or overshoot is observed
  due to longer reference trace lengths.

---

## Testing and Validation

Recommended checks:

- Measure TCXO output at TP1 relative ground
- Confirm stable frequency across temperature and supply variation
- Observe reference signal integrity at the Si5351 XA pin

No trimming or tuning is required during normal operation.

---

## Summary

This 27 MHz TCXO reference circuit provides a stable, low-noise master clock for the Si5351.
It isolates the oscillator from board-level noise sources and ensures consistent RF generation
performance across operating conditions.
