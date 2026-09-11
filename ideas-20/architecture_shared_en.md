---
course: COMP4447 / COMP5047
title: "Shared hardware / firmware contract — all 20 ideas"
updated: 2026-09-12
lang: en
aliases:
  - architecture shared EN
  - shared stack
---

# Shared architecture contract (all 20 ideas)

> **中文**：[[architecture_shared_cn]]
> **Use this page first.** Every idea below reuses the same MCU, firmware layout, pin contract, fail-mode grammar, and week plan. The per-idea packs only document **what is different**: sensors, actuators, enclosure, FSM, demo.

Pass artefact is always: **one MCU + ≥2 sensor types + ≥1 non-screen actuator + fabricated enclosure**. Apple Vision Pro is a **second station**. Headset gaze never fires a motor, pump, latch, or valve.

---

## 1. Why a shared stack

Five MCS students, Weeks 7–13 only. Tutor must approve a pivot (proposal was due W4). You cannot learn a new MCU family, a new CAD tool, and a new cloud stack in parallel. So:

| Decision | Choice | Why |
|---|---|---|
| MCU | **ESP32-S3** DevKitC-1 (N8R8 or N16R8), USB-C, pins pre-soldered | I2C, UART, ADC1, LEDC PWM, WiFi, optional camera/I2S. Tutorial 3–8 already use ESP32. |
| Firmware | **ESP-IDF + FreeRTOS, C** | Matches the old bollard pack. Keep Arduino out of the submitted tree so the proposal and the repo agree. |
| Actuation | **MG90S** (180°, metal gear) and/or **5 V pump + logic-level N-MOSFET** | Week 7 motors. SG90 is too weak in wind. |
| Night / dusk | **BH1750** (or VEML7700) on I2C | Same “smart lamp” lineage as Week 4. |
| Presence | **LD2410C** mmWave on UART | Range-gated; better than PIR for a 1–5 m demo. |
| Climate | **DHT22** and/or **DS18B20** | Heat, water, mound, hive, soil analogs. |
| Credit API | **BoM / Open-Meteo**, **image classifier**, or **BirdNET-class** sound | Week 8. Not ChatGPT as the product. |
| Enclosure | **Fusion 360 → FDM PETG** (timber where the ecology is wood/rock) | Week 5/9. Form **is** the ecology. |
| Power | **USB-C bench** for Week 13; 18650 + buck as documented v2 | Do not make solar the demo. |
| Network | WiFi STA for APIs; **BLE = maintainer log only** | Habitat does not need a phone UI. |

Cloud / phone is **not** in the runtime stack. Atlas of Living Australia / iNaturalist / DCCEEW are **evidence**, not live queries.

---

## 2. One-line architecture (every idea)

```
WORLD (named ecology)
   → SENSE (≥2 types: analogue, I2C, UART, or I2S)
   → FUSE (ESP32-S3 / ESP-IDF, persist + hysteresis)
   → FSM (policy lives in exactly one task)
   → ACTUATE (servo / pump / MOSFET / flag — no screen)
   → POWER (USB bench; 18650 stretch)
DEMO = campus analog + playback / stuffed body / jug. Never a live listed animal.
```

Trust boundary: nothing in the habitat *needs* a network. If the Credit API dies, the FSM has a documented **fail-open / fail-closed / fail-off** that encodes justice, not a spinner.

---

## 3. Default pin contract (ESP32-S3 DevKitC-1)

Exact pins depend on the board silk. Treat this as the **group contract**. Change a pin only in the idea pack, and only if a camera/I2S board forces it.

**Tutorial 6 rule:** WiFi kills **ADC2**. All analogue sensors (turbidity, soil, mic envelope, battery) sit on **ADC1**.

| Function | Bus | Default GPIO | Notes |
|---|---|---|---|
| I2C SDA / SCL (BH1750, MLX90614, VEML7700, optional OLED debug) | I2C0 | **8 / 9** | 3.3 V, 4.7 kΩ pull-ups if the module lacks them |
| LD2410 UART TX/RX (module TX → MCU RX) | UART1 | **17 / 18** | Often 256000 baud. Some modules are 5 V logic → **level-shift** |
| 1-wire (DHT22 data, or DS18B20 DQ) | GPIO | **4** | 4.7 kΩ to 3.3 V for DS18B20 |
| Extra 1-wire / DHT if two climate buses | GPIO | **10** | Second DS18B20 can share the same 1-wire bus |
| Analogue 1 (turbidity / soil / envelope / TDS) | ADC1 | **1** (ADC1_CH0) | 0–3.3 V only |
| Analogue 2 (battery sense / second optical) | ADC1 | **2** | Optional |
| Primary actuator MOSFET / pump / LED | LEDC | **6** | Logic-level N-FET (AO3400 / IRLML2502 class) |
| Primary servo (MG90S) | LEDC 50 Hz | **7** | 5 V on the servo rail, **common GND** |
| Second servo (vent, flag, lid, flap) | LEDC 50 Hz | **11** | |
| Maintainer amber LED | LEDC / GPIO | **5** | Underside / human-only. Must not light the habitat |
| Limit / end-stop / float / boot-wash done | GPIO in | **12** | INPUT_PULLUP |
| Spare / camera PWDN / I2S WS | — | **13+** | See idea pack |
| USB | USB-C | — | Data cable, not charge-only |

3.3 V and GND shared. Add **100 nF + 10 µF** on the 3.3 V rail next to the MCU. Servo and pump on the **5 V** rail; never from the ESP32 3.3 V pin.

**Do not sample ADC while a servo is moving** (motor artefact). `task_actuate` raises `ctx.motor_busy`; sense tasks skip or discard that window.

---

## 4. Power budget (order of magnitude, USB bench)

| State | Draw | Implication |
|---|---|---|
| Idle sense (I2C + UART + ADC, actuators parked) | tens of mA | Default is *off / closed / slack* — sensing still runs |
| One MG90S holding | ~10–20 mA | Park mechanically where you can; don’t PWM-fight |
| MG90S moving | 200–500 mA spike, <1 s | Brown-out risk on cheap USB; use a 2 A supply |
| 5 V micro-pump | 100–400 mA | MOSFET + flyback diode |
| ESP32-S3-CAM + WiFi TX | 200–400 mA bursts | Size the 5 V rail for this if you use a camera |
| USB bench | unlimited for Week 13 | Document 18650 + buck as v2, not the demo |

---

## 5. Firmware layers (ESP-IDF)

```
┌─────────────────────────────────────────────────────────────┐
│  Optional after the demo path works                         │
│  TinyML · ESP-DSP FFT · BLE log dump · visionOS companion   │
├─────────────────────────────────────────────────────────────┤
│  App: state machine + actuator policy                       │
│  (named states live only here — idea pack lists them)       │
├─────────────────────────────────────────────────────────────┤
│  Context fusion                                             │
│  persist · hysteresis · range gates · NVS cache · faults    │
├─────────────────────────────────────────────────────────────┤
│  Drivers                                                    │
│  i2c · uart · adc · 1-wire · ledc PWM · gpio · wifi sta     │
├─────────────────────────────────────────────────────────────┤
│  ESP-IDF · FreeRTOS · ESP32-S3                              │
└─────────────────────────────────────────────────────────────┘
```

**Policy lives in `task_fsm` only.** Drivers do not fade LEDs, start pumps, or “help” with a second timeout.

### Shared FreeRTOS tasks

| Task | Period (typical) | Reads | Writes |
|---|---|---|---|
| `task_sense_*` | 20–1000 ms | bus | fields in `ctx` |
| `task_api` | 1–10 min | WiFi / HTTP | `ctx.forecast_*`, `ctx.class_id`, `ctx.api_ok` |
| `task_clock` | 1 s | NVS sunset + uptime | `ctx.is_night`, `ctx.civil_twilight` |
| `task_fsm` | 50 ms | `ctx` | `cmd.*` |
| `task_actuate` | 20 ms | `cmd` | LEDC / GPIO; sets `ctx.motor_busy` |
| `task_maint` | 1 s | faults, tank empty, stall | `cmd.amber` |

### Shared `ctx` fields (every idea adds its own)

```
api_ok            false → follow the idea’s fail-* rule
fault             sensor timeout, brown-out, empty tank, stall
motor_busy        servo/pump in motion; ADC discard window
is_night          cached BoM sunset–sunrise
civil_twilight    lux band and/or sun-angle cache
maintainer        amber pulse; never a wildlife claim
```

NVS: cache sunset, last good forecast, calibration. If radio drops, **do not** invent a forecast.

### Suggested repo layout (copy per idea, or one repo with `Kconfig` idea select)

```
firmware/
  main/
    app_main.c
    ctx.h
    fsm.c / fsm.h          ← only file that encodes justice
    drivers/lux.c mmwave.c climate.c analog.c pump.c servo.c wifi_api.c
    nvs_cache.c
  CMakeLists.txt
  sdkconfig.defaults
cad/                       ← Fusion + STEP + 3MF
docs/                      ← ESD tables, evidence, video script
visionos/                  ← after Week 9 only
```

---

## 6. Fail-mode grammar (write this in the report)

Pick **one** per actuator and never mix slogans with code:

| Grammar | Meaning | Use when |
|---|---|---|
| **Fail-off** | Pump / mist / light stays **off** | Heat refuge, drip, hydroperiod — false ON is harm |
| **Fail-open** | Gap / runway stays **open** for the named beneficiary | Possum, bandicoot, penguin-sized body if the classifier dies |
| **Fail-closed** | Gap stays **shut** to the threat | Cat-class bodies; dog-side of a penguin gate; bait cover |
| **Fail-down** | Shutter / rope / shade goes to the **protective** pose | Glass-strike, fruit-net slack, boot-gate latch |
| **Fail-park** | Vanes / rock / rope stop moving | Wind, rain, animal already on the object |

“Justice in the state machine” means: the fail-* row is a **transition**, not a caption.

---

## 7. Shared five-person split and remaining weeks

| # | Owns | Week 7–9 | Week 10–13 |
|---|---|---|---|
| 1 | Sensors, power, analogue front-end | Bring-up, calibration | Night/dusk trials |
| 2 | ESP-IDF FSM | Six states on the bench | Hysteresis, API-down path |
| 3 | CAD + actuators | Print v1, servo/pump | Print v2 enclosure, gaskets |
| 4 | Evidence, docs, video | ESD tables, citations | 3–5 min video, manual |
| 5 | visionOS **after W9** *or* PM + evidence | Do not start headset first | Overlay registered to the object |

| Wk | Taught | Architecture job |
|---|---|---|
| 7 | Motors / haptics | Servo or pump moves on a **threshold**, not a button UI |
| 8 | APIs | BoM **or** image **or** BirdNET; cache in NVS |
| 9 | Tangible / mid-check | **One key feature without a headset** |
| 10 | Wearable | Headset as inquiry; optional haptic band for **humans only** |
| 11 | Gaze | Gaze-dwell **reveals**; never actuates |
| 12 | Mixed reality | Overlay registered to the fabricated object |
| 13 | Showcase | Physical demo first, 3–5 min video |

Haptic band, if used, is for the **human maintainer / visitor**. Nothing is worn by wildlife.

---

## 8. Shared cart (order once, then add the idea’s delta)

Buy **two** of every demo-critical module. From Australia: Taobao / Cainiao or a forwarder; 10–21 days. USB-C must be a **data** cable.

**Always (MUST):** 2× ESP32-S3 DevKitC-1; BH1750; LD2410C; 2× MG90S 180° metal; logic-level N-MOSFET + flyback diode; amber LED; jumper + breadboard; PETG; 5 V 2 A USB supply.

**Usually (SHOULD):** DHT22; DS18B20 ×2 waterproof; 18650 + 5 V/3.3 V buck; level shifter; limit switches.

**Per-idea delta** is listed in each pack (pump, camera, I2S mic, turbidity LED+photodiode, soil moisture, ToF, anemometer, tank).

**Never for Week 13:** 192 kHz I2S FFT as the only detector; phone app as the interface; solar as the only power; live listed animals; ultrasound “pest” speakers; ChatGPT as the Credit API.

---

## 9. How to read a per-idea pack

Each pack uses the same skeleton:

1. **Challenge + non-human stakeholders** — who the FSM is for.
2. **One-line architecture + map + data-flow table.**
3. **Hardware** — layers, pin overrides, enclosure, power, BOM delta.
4. **Software** — tasks, `ctx` extras, full FSM, hysteresis, fail-*.
5. **Implementation plan** — week-by-week acceptance tests, five-person jobs, Week 13 demo script.
6. **Vision Pro** — inquiry overlay; cannot fire actuators.
7. **Risks / honesty** — campus analog, no population-recovery claim.

ESD (Expected–Sensed–Desired) is written for the **non-human** in the report. Several ideas need **two** ESD tables (conflicting desired states).
