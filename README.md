# ZdeHavCANdesignTCHG

> **Klipper CAN toolhead board — STM32G0B1CBT6 + TMC2209**

Custom toolhead board design for Klipper-based 3D printers. The board communicates over CAN bus and is designed for direct mounting on the print head (toolchanger or standard mount).

---

## Photos

<table>
  <tr>
    <td><img width="388" height="361" alt="image" src="https://github.com/user-attachments/assets/cf425003-1f72-4b1b-8aba-2ac3d5431a16" /></td>
    <td><img width="362" height="324" alt="image" src="https://github.com/user-attachments/assets/a506f216-4fbe-431e-9e24-2fa7718a85c5" /></td>
    <td><img width="427" height="431" alt="image" src="https://github.com/user-attachments/assets/b2000acc-c9c4-4a00-93d6-e230cc35ee95" /></td>
  </tr>
</table>

---

## Overview

| Parameter | Value |
|---|---|
| MCU | STM32G0B1CBT6 (Cortex-M0+, 64 MHz, 128 KB Flash, LQFP-48) |
| CAN transceiver | SN65HVD230 (3.3 V, 1 Mbit/s, SOIC-8) |
| Stepper driver | TMC2209 (UART) |
| USB | USB-C (D+ / D−, DFU support) |
| Power input | 5 V via USB-C |
| Logic voltage | 3.3 V |
| Crystal | 16 MHz / 10 pF (HSE) |
| PCB | 4-layer |
| Schematic rev. | 1.0 (2026-02-02) |
| Firmware | Klipper (CANBUS UUID) |

---

## Features

- **CAN bus** — SN65HVD230, 120 Ω termination resistor (R26), slope-control resistor on RS pin
- **TMC2209** — STEP / DIR / EN / DIAG, UART via USART2 (`USART2_TX_2209_PDN`), SpreadCycle/StealthChop
- **Thermistors** — 3× ADC inputs (THERM-1, THERM-2, THERM-3)
- **Heater** — PWM output (`TIM3_HE`) for hotend heater
- **Fan** — PWM output (`TIM3_FAN`) for part cooling fan
- **NeoPixel** — 1× WS2812B-Mini (5 V), GPIO output `GPIO_NEOPIXEL`
- **Piezo buzzer** — GPIO output `GPIO_PIEZO` via dedicated subsheet
- **SWD** — SWDIO / SWCLK for debug (Black Magic Probe / J-Link)
- **BOOT0** — SPDT switch (SW1) for toggling DFU / normal boot
- **Filtered VREF** — ferrite bead (FB1) + bypass capacitor array (C1–C8) for clean analog VREF

---

## Schematic Structure

| Sheet | File | Contents |
|---|---|---|
| Main | `ZdeHavCANdesignTCHG.kicad_sch` | MCU, CAN transceiver, crystal, NeoPixel, SWD, BOOT0 |
| Power | `usbc.kicad_sch` | USB-C connector, 5 V / 3.3 V power, CAN input |
| Piezo | `piezo.kicad_sch` | Piezo buzzer and driver |
| Extruder | `extruder.kicad_sch` | TMC2209, extruder connector |
| TH+HT | `thermistorsandheaters.kicad_sch` | Thermistors (3×), heater, fan MOSFETs |

---

## Pinout MCU (key functions)

| STM32 Pin | Function |
|---|---|
| PD0 | FDCAN1_RX |
| PD1 | FDCAN1_TX |
| PA0 / PA1 | ADC1_IN0 / ADC1_IN1 (THERM-1, THERM-2) |
| PC6 | TIM3_CH1 — heater PWM |
| PC7 | TIM3_CH2 — fan PWM |
| PA13 / PA14 | SWDIO / SWCLK (SWD) |
| PA14 / BOOT0 | BOOT0 (switch SW1) |
| PF0 / PF1 | HSE IN / OUT (16 MHz crystal) |

> See schematic for full pin mapping.

---

## Firmware — Klipper

The board is intended to run as a Klipper CAN node.

Example `printer.cfg` snippet:

```ini
[mcu head0]
canbus_uuid: <YOUR_UUID_HERE>

[extruder]
step_pin: head0:PD1
dir_pin: head0:PD0
# ... fill in remaining pins per your wiring

[temperature_sensor toolhead_NTC]
sensor_type: NTC 100K MGB18-104F39050L32
sensor_pin: head0:PA0
```

---

## Manufacturing

A ready-to-order fabrication archive **`CANTCHG28.zip`** is available in the project files. This file is specifically prepared for **JLCPCB** (4-layer stackup) and contains all necessary files (Gerber, BOM, CPL) for production.

---
## Dependencies

- [KiCad 9.0](https://www.kicad.org/)
- [Klipper firmware](https://github.com/Klipper3d/klipper)

---

## License

This hardware design is released as open-source under the **MIT** license.

---

*Design: Zdeněk Havlát — [zdehav.cz](https://zdehav.cz)*
