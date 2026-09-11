# Hardware stack — Wildlife-First Adaptive Path Light

Print or paste **Figure H1** (`Architecture_map.svg`) into the proposal appendix. This file is the parts-and-interfaces design the hardware owners can order against.

Principle: three sensor types, two non-screen actuators, one MCU, no phone in the loop. Demo-critical path is analog frequency-division into ADC — not 192 kHz I2S FFT.

## Stack (outside → silicon)

| Layer | Parts | Interface to ESP32-S3 | Notes |
|---|---|---|---|
| Enclosure | PETG FDM bollard, downward hood, gasket, printed 180° blade | — | Houses electronics. Blade occludes bush azimuth, not the path. Printable MG90S shield: `cad/` in the project root. |
| Power | USB-C bench supply **or** 18650 + buck (5 V / 3.3 V). Solar top-up = stretch | 5 V in, 3.3 V rail | Semester demo runs on USB. Document IP/solar as v2. |
| Compute | ESP32-S3 DevKit (or XIAO ESP32-S3) | — | I2C, UART, ADC, LEDC PWM, GPIO. BLE optional, maintainer-only. |
| Sense — lux | BH1750 **or** VEML7700 | I2C (SDA/SCL) | Night vs dawn/spill. Disables LED above threshold. |
| Sense — presence | LD2410 mmWave | UART (typically 256000 baud) | Range-gated walker ~5 m. 1 s persistence. Prefer over PIR. |
| Sense — bat band | MEMS mic (e.g. Knowles SPH0641LU4H-1) **+ off-the-shelf analog frequency-division / envelope module** | ADC (GPIO), 3.3 V analog | Dual gates: **~10–15 kHz** (*A. australis*) and **~25–35 kHz** (*C. gouldii*). Do not design 192 kHz sampling as the demo. |
| Actuate — path light | 2200 K or PC-amber LED module (~200 lm), N-MOSFET, flyback diode, current-limit | LEDC PWM | Fade 1.5 s up / 5 s down. Hard illuminance floor while a person is present. |
| Actuate — bush shield | MG90S servo + printed 180° blade | LEDC PWM (50 Hz servo) | Default closed toward bush. Tightens onto path when bat-band fires. Do not move during a detection window. |
| Actuate — maintainer | Underside amber LED, current-limit resistor | GPIO PWM | Slow pulse. Must not light the habitat. |
| Demo only | Phone / speaker / bat-detector playback of NSW calls | Into the mic / FD input | Assessor stimulus. Not a claim of a captured animal. |

## Suggested ESP32-S3 pin map (DevKit-class)

Exact pins depend on the board. Treat this as the contract; change only if a pin conflicts.

| Function | Bus | Suggested pins (DevKit-S3) |
|---|---|---|
| BH1750 SDA / SCL | I2C0 | GPIO 8 / 9 |
| LD2410 TX / RX | UART1 | GPIO 17 / 18 |
| Bat-band envelope | ADC1 | GPIO 1 (ADC1_CH0) |
| Path LED MOSFET gate | LEDC | GPIO 6 |
| Servo signal | LEDC | GPIO 7 |
| Underside amber | LEDC / GPIO | GPIO 5 |
| Battery sense (optional) | ADC1 | GPIO 2 |
| Status / spare | GPIO | GPIO 4 |

3.3 V and GND shared. LD2410 is 5 V logic on some modules — **level-shift UART** if required. MOSFET gate: 3.3 V logic-level N-FET (e.g. IRLZ44N is overkill; AO3400 / IRLML2502 class is enough for a 200 lm LED). Add a 100 nF + 10 µF on the 3.3 V rail near the MCU.

## Power budget (order-of-magnitude, night idle)

| State | Draw (approx.) | Why it matters |
|---|---|---|
| Idle sense (lux + mmWave + ADC, LED off, servo holding) | tens of mA | Default is darkness; sensing still runs. |
| Path LED on | 100–400 mA depending on module | Dominates when a walker is present. |
| Servo moving | brief 200–500 mA spikes | Do not sample ADC during motion. |
| USB bench | unlimited | Week 13 demo. |

## What not to buy for Week 13

- Do not depend on a 192 kHz capable I2S pipeline as the only bat detector.
- Do not add a phone app as the primary interface.
- SG90 is weaker than MG90S for a blade in wind; buy MG90S.
- Duplicate “solar PSU” modules: one 18650 + buck is enough on the bench.

## Order this week (minimum)

Full cart, SKUs, and Taobao links: [`Hardware_BOM.md`](Hardware_BOM.md) · spreadsheet [`Hardware_BOM.csv`](Hardware_BOM.csv).

ESP32-S3 board, BH1750 or VEML7700, LD2410C, analog bat path (MAX9814 **plus** 40 kHz receiver + LM358 — there is no cheap ready-made FD module), 2200 K or PC-amber LED + logic-level MOSFET, MG90S, amber LED, USB data cable, jumper wire, breadboard, PETG filament.
