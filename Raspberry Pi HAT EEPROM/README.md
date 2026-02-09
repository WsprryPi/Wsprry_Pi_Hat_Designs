# HAT ID EEPROM Circuit

## Overview

This block implements a **Raspberry Pi HAT identification EEPROM** used to automatically identify
the attached board at boot time.

The circuit allows the Raspberry Pi to:

- Detect the presence of the HAT
- Read board identification and configuration data
- Automatically configure GPIO functions
- Optionally load Linux drivers based on the HAT descriptor

This block is **non-RF** and operates independently of the RF signal chain.

---

## Intent and Design Goals

Raspberry Pi HAT designs rely on a small I²C EEPROM connected to the reserved **ID_SD** and **ID_SC** pins.
At boot, the Raspberry Pi firmware interrogates this EEPROM to determine board identity and capabilities.

This circuit is designed to:

- Fully comply with the Raspberry Pi HAT specification
- Require no configuration or tuning in normal operation
- Protect the EEPROM contents after programming
- Remain electrically isolated from the RF circuitry

---

## Circuit Description

The circuit consists of a dedicated I²C EEPROM with pull-up resistors and optional write protection.

### Functional sections

- **EEPROM device (U2)**  
  Stores the HAT identification data.

- **I²C pull-up resistors (R1, R2)**  
  Provide the required pull-ups on SDA and SCL for the ID bus.

- **Local decoupling capacitor (C1)**  
  Stabilizes the EEPROM supply during I²C transactions.

- **Write-protect jumper (JP1)**  
  Allows permanent write protection after programming.

---

## Component Selection Rationale

### EEPROM

- **CAT24C32 (or compatible 24C32 device)**
  - I²C-compatible EEPROM
  - Adequate capacity for HAT descriptor data
  - Widely available and low cost

Address pins A0–A2 are left unconnected to select the default address required by the HAT specification.

### Pull-up resistors

- **R1, R2 = 3.9 kΩ**
  - Compliant with Raspberry Pi HAT electrical requirements
  - Ensures reliable I²C signaling at boot

### Decoupling capacitor

- **C1 = 0.1 µF**
  - Local supply stabilization
  - Placed close to the EEPROM device

### Write-protect jumper

- **JP1**
  - Open: EEPROM is writable
  - Bridged: Write protection enabled (WP tied high)

---

## Performance Expectations

- Reliable EEPROM detection during Raspberry Pi boot
- No interaction with or impact on RF circuit performance
- Stable operation across normal temperature and voltage ranges

This circuit performs no time-critical or high-speed function.

---

## Integration Notes

### Placement

- Place the EEPROM close to the Raspberry Pi header.
- Keep SDA and SCL traces short and routed together.
- Avoid routing RF signals near the ID bus traces.

### Programming

- Program the EEPROM prior to final assembly or in-circuit before enabling write protection.
- After verification, bridge **JP1** to prevent accidental overwrites.

### Signal Chain Context

```text
Raspberry Pi ID Bus (ID_SD, ID_SC)
    → HAT ID EEPROM
    → Boot-Time Board Identification
```

---

## Testing and Validation

Recommended checks:

- Confirm EEPROM is detected at boot
- Verify correct HAT identification using Raspberry Pi tools
- Confirm EEPROM is not writable after WP is enabled

No calibration or tuning is required.

---

## Summary

This HAT ID EEPROM circuit provides standards-compliant board identification for Raspberry Pi HAT designs.
It enables automatic configuration at boot while remaining isolated from the RF circuitry.
Once programmed and write-protected, it requires no further interaction.

---

## Reference

Raspberry Pi Ltd. *Raspberry Pi HAT+ Specification: A Guide to Designing Hardware-Attached-on-Top of a Raspberry Pi.* Build date: 2024-12-05. Build version: 6df06cc-clean. [https://datasheets.raspberrypi.com/hat/hat-plus-specification.pdf](https://datasheets.raspberrypi.com/hat/hat-plus-specification.pdf)
