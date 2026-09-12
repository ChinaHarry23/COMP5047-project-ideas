# Hardware BOM — Catchment bin

Prices are typical Taobao ranges (CNY), late 2026. Buy **two** of every demo-critical module. USB-C must be a **data** cable. From Australia: Taobao / Cainiao or a forwarder; 10–21 days.

Shared cart for this project only. Buy two of every demo-critical module.

| Priority | Meaning |
|---|---|
| **MUST** | Order this week. Demo fails without it. |
| **SHOULD** | Same parcel. |
| **A-only / B-only** | Approach delta. If you build the recommended one-box, buy both. |
| **STRETCH** | After W9. |
| **AVOID** | Fails rubric or ethics. |

---

## MUST (shared)

| # | Part | Qty | Spec | Why | Est. ¥ |
|---|---|---|---|---|---|
| M1 | ESP32-S3 DevKitC-1 N8R8/N16R8 | 2 | USB-C, pins pre-soldered | MCU | 50–200 |
| M2 | BH1750 | 2 | 3.3 V module | Lux | 5–12 |
| M3 | LD2410C kit | 2 | Hi-Link | Rim presence | 15–30 |
| M4 | MG90S 180° **metal** | 3 | Not 360°, not SG90 | Lid + weir + spare | 15–25 |
| M5 | Logic-level N-MOSFET + flyback | 5 | AO3400 class | LED / piezo | 5 |
| M6 | Amber LED + resistor | 5 | 3 mm | Maintainer | 2 |
| M7 | White LED + photodiode | 2 | For turbidity | Analog water | 5–15 |
| M8 | Jumper + breadboard | 1 set | | Bring-up | 15 |
| M9 | PETG 1 kg | 1 | Sandstone / grey | Enclosure | 50–80 |
| M10 | 5 V 2 A USB supply + **data** cable | 1 | | Bench | 20–40 |

## SHOULD

| # | Part | Qty | Why |
|---|---|---|---|
| S1 | ESP32-S3-CAM or OV2640 | 1–2 | Image API |
| S2 | Capacitive soil sensor | 2 | Rain proxy |
| S3 | Level shifter | 2 | LD2410 5 V UART |
| S4 | Limit switch | 3 | Lid throat |
| S5 | 18650 + 5 V/3.3 V buck | 1 | Documented v2 |
| S6 | Stuffed gull + balloon analog + banana | 1 | Week 13 |
| S7 | Two food trays / printed cisterns | 2 | Tea+glitter demo |

## Approach extras

| # | Part | Approach | Why |
|---|---|---|---|
| A1 | Fiducial stickers / printed marks | A | visionOS registration |
| B1 | VL53L0X | B | Height vs person |
| B2 | Piezo disc 2–3 kHz | B stretch | Capped click — **not** ultrasonic |

## AVOID

| Part | Why |
|---|---|
| Ultrasonic bird-repel / “dog trainer” transducers | Ethics + non-targets + unit ACI |
| Phone as primary UI | Pass fail |
| Charge-only USB-C | Cannot flash |
| SG90 | Too weak for a lid in a demo crowd |
| Live birds, real harbour salt, poison bait | Ethics / hardware death |

Official shops (bookmark): Espressif 乐鑫, Hi-Link 海凌科, Seeed 矽递.
