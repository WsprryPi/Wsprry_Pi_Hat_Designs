# HF LPF Relay Control via I2C (Raspberry Pi + Si5351)

This document defines the **pin-by-pin schematic wiring** and **layout guidance** for controlling a 4-position HF LPF bank using:

- Raspberry Pi (I²C master)
- Existing Si5351 on the same I²C bus
- TCA9534A I²C GPIO expander (no interrupts used)
- ULN2003A relay driver
- Three Omron G6K-2F-Y-DC5 DPDT relays

Design goals:

- LOW band group selected by default (all relays de-energized).
- Unused branch or filter input is hard-grounded.
- 5 V relay coils, 3.3 V I²C logic.
- 2-layer PCB, JLCPCB assembly-friendly.

---

## 1. Raspberry Pi Header Connections

Using the Raspberry Pi I²C1 bus.

| Pi Header Pin | Signal | Net Name |
| --- | --- | --- |
| 1 | 3.3 V | `+3V3` |
| 2 or 4 | 5 V | `+5V` |
| 3 | GPIO2 / SDA1 | `I2C_SDA` |
| 5 | GPIO3 / SCL1 | `I2C_SCL` |
| 6 | GND | `GND` |

No interrupt line is used from the GPIO expander.

---

## 2. I2C Pull-Up Provisioning

Add footprints for I²C pull-ups, **DNP by default**.

| Reference | Value | Connects |
| --- | --- | --- |
| R_SDA | DNP | `I2C_SDA` to `+3V3` |
| R_SCL | DNP | `I2C_SCL` to `+3V3` |

---

## 3. TCA9534A GPIO Expander (3.3 V)

Address pins tied low → I²C address **0x38**.

| Pin | Name | Connect To | Net |
| --- | --- | --- | --- |
| 1 | A0 | GND | `GND` |
| 2 | A1 | GND | `GND` |
| 3 | A2 | GND | `GND` |
| 4 | P0 | ULN IN1 | `RELAY_K1_CTL` |
| 5 | P1 | ULN IN2 | `RELAY_K2_CTL` |
| 6 | P2 | ULN IN3 | `RELAY_K3_CTL` |
| 7 | P3 | Unused | NC |
| 8 | GND | Ground | `GND` |
| 9 | P4 | Unused | NC |
| 10 | P5 | Unused | NC |
| 11 | P6 | Unused | NC |
| 12 | P7 | Unused | NC |
| 13 | INT | Not connected | NC |
| 14 | SCL | Pi SCL | `I2C_SCL` |
| 15 | SDA | Pi SDA | `I2C_SDA` |
| 16 | VCC | 3.3 V | `+3V3` |

---

## 4. ULN2003A Relay Driver

| Pin | Name | Connect To | Net |
| --- | --- | --- | --- |
| 1 | IN1 | TCA P0 | `RELAY_K1_CTL` |
| 2 | IN2 | TCA P1 | `RELAY_K2_CTL` |
| 3 | IN3 | TCA P2 | `RELAY_K3_CTL` |
| 8 | GND | Ground | `GND` |
| 9 | COM | Clamp diode common | `+5V` |
| 16 | OUT1 | K1 coil low | `K1_COIL_LO` |
| 15 | OUT2 | K2 coil low | `K2_COIL_LO` |
| 14 | OUT3 | K3 coil low | `K3_COIL_LO` |

### Mandatory Input Pulldowns

| Reference | Value | Connects |
| --- | --- | --- |
| R_PD1 | 47k–100k | `RELAY_K1_CTL` → `GND` |
| R_PD2 | 47k–100k | `RELAY_K2_CTL` → `GND` |
| R_PD3 | 47k–100k | `RELAY_K3_CTL` → `GND` |

---

## 5. Omron G6K-2F-Y Relay Pinout

| Pin | Function |
| --- | --- |
| 1 | Coil |
| 2 | NO (Pole A) |
| 3 | COM (Pole A) |
| 4 | NC (Pole A) |
| 5 | NO (Pole B) |
| 6 | COM (Pole B) |
| 7 | NC (Pole B) |
| 8 | Coil |

---

## 6. GPIO Truth Table → Band Mapping

`0` = relay de-energized, `1` = energized.

| K1 | K2 | K3 | Active LPF | Bands |
| --- | --- | --- | --- | --- |
| 0 | 0 | X | LPF_A | 160 m / 80 m |
| 0 | 1 | X | LPF_B | 60 m / 40 m |
| 1 | X | 0 | LPF_C | 30 m / 20 m |
| 1 | X | 1 | LPF_D | 15 m / 12 m / 10 m |

---

## 7. Layout Checklist (2-layer HF)

- Bottom layer must be a solid ground plane.
- Place relays between LPF cells, not inline with inductors.
- Keep RF trunks short and join only at final nodes.
- One ground via per shunt capacitor pad.
- Keep I²C and coil routing away from RF paths and inductors.
- Decouple relay +5 V locally (0.1 µF + 4.7–10 µF).
- Add RF test pads at RF_IN, RF_OUT, LOW_OUT, HIGH_OUT.

---

## 8. Expected Power-Up State

- All relays off.
- LOW band group active.
- LPF_A (160 m / 80 m) selected.
- All unused paths grounded.
