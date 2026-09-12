# Hardware stack — Catchment bin (both approaches)

Principle: one MCU family, one print, two firmware personalities. Demo-critical path is **lid / weir + analog turbidity + image class**, not a headset.

Pin contract is in the table below. **ADC1 only** if WiFi is on (Tutorial 6: WiFi kills ADC2).

---

## Shared layers (outside → silicon)

| Layer | Parts | Interface | Notes |
|---|---|---|---|
| Enclosure | PETG “catchment-mouth” bin, sandstone colour, two trays, lid, optional flag | — | Form is street furniture at a drain, not a gadget |
| Power | USB-C 5 V 2 A bench; 18650 + buck = v2 | 5 V / 3.3 V | Week 13 on USB |
| Compute | ESP32-S3 DevKit **or** ESP32-S3-CAM | — | CAM if you want on-board JPEG; else OV2640 on a second board |
| Sense — lux | BH1750 | I2C 8/9 | Day (gull hours) vs night |
| Sense — presence | LD2410C | UART 17/18 | Rim gate; 1 s persist |
| Sense — water | LED + photodiode turbidity | ADC1 GPIO 1 | Approach 1 must; Approach 2 should (same PCB) |
| Sense — rain proxy | Capacitive soil pad **or** tip-bucket | ADC1 GPIO 2 / GPIO | First-flush start |
| Actuate — weir / lid | MG90S ×1–2 | LEDC GPIO 7, 11 | 5 V rail, common GND |
| Actuate — maintainer | Underside amber | GPIO 5 | Must not light habitat |
| Demo | Stuffed gull, balloon analog, banana, tea+glitter | — | No live animals |

---

## Pin map (DevKit-S3 contract)

| Function | Bus | GPIO | A | B |
|---|---|---|---|---|
| BH1750 SDA/SCL | I2C0 | 8 / 9 | ✓ | ✓ |
| VL53L0X (optional, same I2C) | I2C0 | 8 / 9 | — | ✓ |
| LD2410 TX/RX | UART1 | 17 / 18 | optional | ✓ |
| Turbidity | ADC1 | 1 | ✓ | ✓ |
| Rain proxy | ADC1 | 2 | ✓ | optional |
| Weir or lid servo | LEDC | 7 | weir | lid |
| Flag / second servo | LEDC | 11 | flag | unused or weir if both FSMs |
| Piezo MOSFET | LEDC | 6 | — | optional |
| Amber | GPIO | 5 | ✓ | ✓ |
| Gap end-stop | GPIO in | 12 | — | ✓ |
| DHT / spare | GPIO | 4 | spare | spare |

Camera boards steal pins — **write the silk in `fsm.h`**. Keep analog on ADC1. Servo and pump/piezo on 5 V, never the ESP32 3.3 V pin. 100 nF + 10 µF on 3.3 V. Level-shift LD2410 if 5 V logic.

**Do not sample ADC while a servo is moving.** `ctx.motor_busy` discards that window.

---

## Approach deltas

### Approach 1 only

Second tray, weir channel, BoM WiFi, visionOS fiducial stickers on the tray rims. No piezo.

### Approach 2 only

Lid throat CAD (gull cannot extract a 10 cm balloon when shut), end-stop, optional piezo. ToF across the mouth.

### Both on one box (recommended)

Print weir **and** lid. `sdkconfig` `CONFIG_APPROACH_B=y` for Pass. Approach 1 overlay still registers to the trays.

---

## Power (order of magnitude)

| State | Draw |
|---|---|
| Idle sense, lid open | tens of mA |
| Servo moving | 200–500 mA spike |
| CAM + WiFi TX | 200–400 mA bursts |
| Piezo click | negligible, 200 ms |
| USB 2 A | Week 13 |

---

## What not to buy

- Ultrasonic bird-repel modules
- 192 kHz I2S “for science”
- Phone as the interface
- Solar as the only power
- Real metaldehyde / real harbour salt / live birds
- A second laptop GPU “VR PC” as the product
