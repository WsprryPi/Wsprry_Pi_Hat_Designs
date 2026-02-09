# RF Isolation & Edge Conditioning Buffer

## Overview

This block implements a **unity-gain RF buffer with edge conditioning** placed immediately after the
Si5351 clock output. Its purpose is to isolate the clock generator from the downstream RF chain
while improving spectral cleanliness and impedance stability.

The buffer provides:

- Electrical isolation between the Si5351 and the RF chain
- Controlled, low-impedance drive into attenuation and filtering stages
- Reduction of fast-edge spectral content prior to filtering
- Stable, repeatable behavior across all HF bands

The design is intended for **HF operation from 160 m through 10 m (1.8 to 30 MHz)**.

---

## Intent and Design Goals

The Si5351 produces a fast-edge digital waveform whose spectral characteristics are strongly
dependent on loading. Directly driving filters or attenuators can result in:

- Increased high-order harmonic content
- Load-dependent amplitude variation
- Sensitivity to layout and impedance changes

This buffer is designed to:

- Present a light, well-defined load to the Si5351
- Provide a low-impedance, well-damped source to the RF chain
- Reduce edge-related broadband energy before attenuation and filtering
- Operate without tuning or adjustment

The block is intentionally **not a gain stage**. It provides approximately unity voltage gain.

---

## Circuit Description

The circuit uses a **single-transistor emitter follower** biased for linear operation and stability.

### Functional sections

- **Input coupling (C1)**  
  AC-couples the Si5351 output while blocking DC.

- **Base bias network (R1, R2)**  
  Establishes a mid-rail operating point for linear signal swing.

- **Base stopper resistor (R3)**  
  Damps fast edges and suppresses parasitic oscillations.

- **Emitter follower (Q1, R5)**  
  Provides current gain and low output impedance with minimal distortion.

- **Optional edge shunt (C4)**  
  Provides additional suppression of VHF/UHF edge energy if required. Default is DNP.

- **Output damping (R6)**  
  Further isolates the buffer from downstream stages.

- **Supply isolation and decoupling (R4, C2, C3)**  
  Prevents supply noise from modulating the RF signal.

- **Output coupling (C5)**  
  Blocks DC and delivers RF to the next stage.

---

## Component Selection Rationale

### Active device

- **MMBT3904**
  - Low-cost, widely available NPN transistor
  - Sufficient ft for HF operation
  - Predictable behavior in emitter-follower configuration

### Resistors

- **R1, R2 = 10 kΩ**
  - Establish mid-rail bias with minimal current draw

- **R3 = 220 Ω**
  - Base stopper for stability and spectral cleanliness

- **R5 = 150 Ω**
  - Sets emitter current and ensures linear operation

- **R6 = 33 Ω**
  - Output damping and isolation

- **R4 = 10 Ω**
  - Supply isolation for local decoupling

### Capacitors

- **C1, C5 = 10 nF**
  - AC coupling with negligible HF attenuation

- **C2 = 1.0 µF, C3 = 0.1 µF**
  - Wideband local supply decoupling

- **C4 = 22 pF (DNP)**
  - Optional edge conditioning for VHF/UHF suppression

---

## Performance Expectations

### Electrical Behavior

- Voltage gain: approximately unity
- Output impedance: low and well-controlled
- HF amplitude: consistent across frequency

### Spectral Behavior

Compared to a direct Si5351 connection, this buffer:

- Reduces high-order harmonic content
- Improves repeatability across board layouts
- Improves effectiveness of downstream attenuation and filtering

This block does not replace proper filtering but improves overall system behavior.

---

## Integration Notes

### Placement in Signal Chain

Recommended placement:

```text
Si5351 CLKx
    → RF Isolation & Edge Conditioning Buffer
    → Fixed Attenuation Pad
    → Primary Low-Pass Filter
    → Broadband MMIC Gain Block
    → Band-Specific Filtering / PA
```

### Operating Assumptions

- Si5351 output current is configured once and left fixed.
- The buffer is driven within normal logic-level swing.
- No adjustment or trimming is required.

### Optional Component Usage

- **C4** should normally remain DNP.
- Populate C4 only if measurement indicates excess high-frequency edge content.

---

## Layout Guidelines

- Place the buffer immediately adjacent to the Si5351 output pin.
- Keep base and emitter traces short.
- Place R3 and R6 close to the transistor pins.
- Provide solid ground connections for R5 and decoupling capacitors.

Good layout is critical for achieving the intended spectral improvements.

---

## Testing and Validation

Recommended checks:

- Observe output spectrum before and after the buffer.
- Confirm amplitude stability across HF bands.
- Verify no oscillation or excessive ringing.

No tuning is required during normal operation.

---

## Summary

This RF Isolation and Edge Conditioning Buffer provides a stable, low-impedance interface between
the Si5351 clock output and the RF signal chain. It improves spectral cleanliness, reduces load
sensitivity, and supports a conservative, repeatable RF gain plan.
