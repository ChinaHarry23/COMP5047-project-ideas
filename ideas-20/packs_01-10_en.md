---
course: COMP4447 / COMP5047
title: "Ideas 1–10 — detailed proposal + hardware/software architecture (EN)"
updated: 2026-09-12
lang: en
aliases:
  - packs 1-10 EN
---

# Proposal + architecture packs — ideas 1–10 (detailed)

> **中文**：[[packs_01-10_cn]] · **Index**：[[00_index_en]] · **11–20**：[[packs_11-20_en]] · **Shared contract**：[[architecture_shared_en]]
> Skeleton: Challenge → Stakeholders → Behaviours → Architecture → Hardware → Software FSM → Implementation plan → Demo → AVP → Risks.
> **Team:** copy from [[00_index_en]] / `TEAM.md`. Tick exactly one PM.
> Pins, power, firmware layers, fail-mode grammar, and the shared cart live in [[architecture_shared_en]]. This file only overrides what is unique.

**Shared five-person split:** (1) sensors/power (2) ESP-IDF FSM (3) CAD + actuators (4) evidence/docs/video (5) visionOS after W9.

---

## 1. Camp-edge heat refuge

### Challenge statement
Grey-headed flying-foxes (*Pteropus poliocephalus*, EPBC vulnerable) roost in Sydney camps. Extreme heat at and above ~42 °C causes mass deaths, especially of pups and lactating females (NSW heat-stress guidance; Welbergen-type literature; January 2026 east-coast mortality). Darkness is not the issue: **heat and shade** are. We do **not** install in a live camp this semester.

### Non-human stakeholders
- **Primary — pups / dependents.** Need evaporative cooling and shade when thresholds are crossed.
- **Secondary — adults.** Need an undisturbed camp; the device must not become a tourist mister.
- **Tertiary — roost trees.** Must not be soaked into rot; mist is short, directed, default **off**.

### Project idea (behaviours)
Hanging PETG “leaf” in a tree analog (heat-box on campus).

1. `IDLE` — sensors on, mist off, leaf open to ambient.
2. `FORECAST_HEAT` — BoM says peak will cross threshold → leaf folds to shade **before** the peak.
3. `LIVE_HEAT` — DHT22 + IR canopy temp in-band → mist PWM, short bursts.
4. `RECOVER` — temps fall → mist off, leaf returns.
5. `FAULT` — tank empty / pump stall → underside LED for a **human** maintainer only.
6. `INHIBIT_COOL` — below threshold, do nothing.

### One-line architecture
Air + canopy heat + forecast → ESP32-S3 → fold leaf and (only if live heat) pulse a pump. Default **off**. Fail-**off**.

```
 WORLD     air heat / RH         canopy surface         6 h forecast / clock
               │                      │                      │
 SENSE      DHT22                  MLX90614 (or lux)      WiFi → BoM / Open-Meteo
               │                      │                      │
 FUSE                    ESP32-S3 / ESP-IDF  six-state
               │                      │                      │
 ACTUATE    MOSFET + 5 V pump      MG90S leaf-shade      maintainer amber
 POWER      USB bench / 18650      gravity tank + float
 DEMO       heat gun / hot box     never a live camp
```

### Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| DHT22 | MCU 1-wire | T_air, RH | live heat band; wet-bulb proxy |
| MLX90614 | MCU I2C | T_canopy | “roost analog” surface, not air |
| BoM / Open-Meteo | WiFi → NVS | T_max next 6 h, heatwave flag | feedforward shade |
| Float / empty | GPIO | tank_ok | inhibit pump |
| FSM | MOSFET | PWM duty, burst 2 s / off 20 s | mist; never continuous |
| FSM | MG90S | fold 0–90° | shade before peak |
| FSM | amber | slow pulse | human only |

### Hardware stack

| Layer | Parts | Bus / notes |
|---|---|---|
| Enclosure | Fusion hanging leaf, bark PETG, tank pocket, drip nozzle aimed at a **fake** canopy, not a person | Form is a leaf, not a gadget box |
| Compute | ESP32-S3 DevKit | shared pins |
| Sense | DHT22 (GPIO 4); MLX90614 on I2C **or** BH1750 if IR slips | IR preferred — air DHT in sun reads high; shade the probe |
| Actuate | 5 V pump + N-MOSFET + flyback; MG90S; underside amber | GPIO 6 pump, GPIO 7 servo, GPIO 5 amber |
| Water | 0.5–1 L tank, gravity feed, float on GPIO 12 | Electronics **above** the tank |
| Net | WiFi STA | BoM / Open-Meteo; cache 6 h |

**Pin overrides.** Default shared map. No LD2410 required (presence is not the story). Spare GPIO 17/18 unused.

**Power.** USB 2 A. Pump dominates. Do not run the pump from the 3.3 V pin.

**BOM delta.** 5 V pump, tank, tubing, MLX90614, float switch. No camera.

**Do not buy.** Camp sprinkler “for Wolli Creek.” NPWS / ethics. No live roost.

### Software stack

Tasks: `task_climate` 1 s · `task_ir` 500 ms · `task_forecast` 10 min · `task_fsm` 50 ms · `task_actuate` 20 ms.

Extra `ctx`: `t_air`, `rh`, `t_canopy`, `t_max_6h`, `tank_ok`, `burst_remaining_ms`.

Hysteresis: enter `LIVE_HEAT` at T_canopy ≥ 38 °C **and** T_air ≥ 35 °C (demo thresholds — report cites 42 °C camp science, then says the analog is cooler). Exit 2 °C below. Forecast shade at T_max_6h ≥ 36 °C analog / 40 °C if you scale the story.

```
                 T_max_6h high
      IDLE ──────────────────────► FORECAST_HEAT (leaf folds, pump still off)
        │                              │
        │  live band                   │ live band
        ▼                              ▼
   INHIBIT_COOL ◄── cool ── LIVE_HEAT (PWM bursts while tank_ok)
        ▲                              │
        └──────── RECOVER ─────────────┘  (pump off, leaf holds then opens)

   any + !tank_ok or stall ──► FAULT (amber, pump forced off)
   api_ok false ──► do not enter FORECAST_HEAT; stay IDLE unless live band
                    Fail-off: never mist on a missing forecast.
```

| State | Leaf | Pump | Rule |
|---|---|---|---|
| `IDLE` | open | off | Default. |
| `FORECAST_HEAT` | folded | **off** | Feedforward is shade, not water. |
| `LIVE_HEAT` | folded | burst PWM | Only with tank_ok. |
| `RECOVER` | hold then open | off | Temps falling. |
| `INHIBIT_COOL` | open | off | Below band. |
| `FAULT` | last pose | **off** | Amber only. |

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Pump bursts on a dummy `LIVE_HEAT` flag; servo folds 90° | 1+3 |
| 8 | Open-Meteo JSON → NVS `t_max_6h`; radio-down leaves pump off | 2 |
| 9 | Heat-box: hairdryer on IR probe folds leaf **before** pump (mid-check) | all |
| 10–11 | Print leaf v2, gasket tank, no leak onto MCU | 3 |
| 11–12 | Optional AVP thermal volume | 5 |
| 13 | Heat-box demo, 3 min; say “not a camp” | 4 |

**Week 13 script.** (1) Show idle leaf. (2) Laptop: cached forecast → leaf folds, nozzle dry. (3) Heat gun on canopy analog → 2 s mist, 20 s off. (4) Unplug tank float → amber, mist stops. (5) Headset optional.

### Vision Pro
Thermal volume around the physical leaf; pinch to scrub 6 h forecast; gaze-dwell “pup vs adult.” **Must not fire the mist.**

### Risks
Ethics/NPWS if anyone proposes a real camp (refuse). Pump leak. DHT in sun reads high (shade the probe). Do not claim population recovery. January 2026 deaths are evidence, not a licence to spray animals.

---

## 2. Brush-turkey mound kiln

### Challenge statement
Australian brush-turkeys (*Alectura lathami*) incubate eggs by farming mound temperature around **33 °C**. Suburban Sydney hosts them; people rake mounds as “mess,” killing the clutch. The bird already has an interface. We join it.

**Literature honesty.** Strong ACI essay; **not** a SoE/KTP headline. Fine for Credit IxD; Distinction needs extra urban-ecology cites, or pick a source-first idea.

### Non-human stakeholders
- **Primary — eggs/embryos.** Stable incubation band.
- **Secondary — the male.** Other operator; vents must not fight him into abandoning the analog.
- **Tertiary — humans with rakes.** A readable “do not rake” flag, not a notification.

### Project idea
Mulch-filled PETG mound analog + aquarium heater **for demo only**.

1. `TOO_COOL` — vents closed; optional heater in the analog only.
2. `IN_BAND` (~32–34 °C) — vents idle; **flag up**.
3. `TOO_HOT` — vents/fan open.
4. `WEATHER_HOLD` — BoM cold rain → do not open vents wide.
5. `PROBE_FAULT` — flag **down** (no false “safe to rake”).
6. `IDLE` — heater off for transport.

### One-line architecture
Two mound-core probes + air/rain forecast → ESP32-S3 → vents/fan + a physical flag. Temperature **is** the dialogue.

```
 WORLD     mound core ×2              air / rain forecast
               │                            │
 SENSE      DS18B20 ×2                   DHT22 + WiFi BoM
               │                            │
 FUSE              ESP32-S3  band FSM
               │                            │
 ACTUATE    MG90S vents + 5 V fan        MG90S flag
 DEMO       sandbox + aquarium heater     28 / 33 / 38 °C
```

### Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| DS18B20 A/B | 1-wire | T_core median | band; disagree >2 °C → fault |
| DHT22 | GPIO | T_air, RH | rain/cold hold |
| BoM | NVS | rain next 6 h | do not dump heat before a front |
| FSM | servo 1 | vent angle | cool only when too hot |
| FSM | MOSFET | fan | assist vents |
| FSM | servo 2 | flag up/down | human message |

### Hardware stack

| Layer | Parts | Notes |
|---|---|---|
| Enclosure | Thatched PETG shell, real mulch, probes in wells, flag arm | Form **is** the mound |
| Sense | Waterproof DS18B20 ×2 on GPIO 4 (shared 1-wire); DHT22 GPIO 10 | Never bury the MCU |
| Actuate | MG90S vent (GPIO 7); MG90S flag (GPIO 11); 5 V fan + MOSFET (GPIO 6) | |
| Demo heat | Aquarium heater on a **timer plug**, not MCU mains | Isolation |

**BOM delta.** 2× DS18B20, 5 V fan, mulch, small sandbox, heater on a separate circuit.

### Software stack

`task_probe` 1 s (median of two) · `task_air` · `task_bom` · `task_fsm` · `task_actuate`.

Hysteresis **±0.5 °C** so vents do not chatter. Disagreeing probes → `PROBE_FAULT`.

```
   TOO_COOL (vents shut, demo heater allowed)
        │  median ≥ 32.5
        ▼
   IN_BAND (flag UP, vents idle) ←→ WEATHER_HOLD (vents capped if rain_front)
        │  median ≥ 34.5
        ▼
   TOO_HOT (vents + fan)
        │  median ≤ 33.5
        ▼
   back to IN_BAND

   missing probe or |A−B|>2 ──► PROBE_FAULT (flag DOWN, fan off)
```

| State | Vents/fan | Flag | Rule |
|---|---|---|---|
| `TOO_COOL` | shut | down | Do not advertise a clutch that is cold |
| `IN_BAND` | idle | **up** | “Do not rake” |
| `TOO_HOT` | open | up | Join the male’s cooling, don’t replace him |
| `WEATHER_HOLD` | capped | up | Forecast rain |
| `PROBE_FAULT` | park | **down** | No false all-clear |
| `IDLE` | park | down | Transport |

Fail-**park**. Fail-**down** on the flag if probes die.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Two servos: vent 0/45/90, flag up/down from serial | 3 |
| 8 | BoM rain flag caps vent angle | 2 |
| 9 | Heater 28/33/38 °C: flag only up in-band (mid-check — strongest artefact) | 1+2 |
| 10–11 | Thatch print, hide wiring in a hollow | 3 |
| 13 | Three-temp demo at 3 m; “analog, not a nest” | 4 |

### Vision Pro
33 °C isotherm volume inside the physical mound; two-hand egg-depth; gaze on flag = “raking now kills the clutch.”

### Risks
Students “help” with a heater in a way the report over-claims. Say clearly: analog, not a real nest. Best Week-13-proof artefact in the set.

---

## 3. Dusk cat-gate for possums

### Challenge statement
Outdoor cats (pets and ferals) are a leading driver of Australian mammal and bird decline (Woinarski / Legge; TAP 2024; DCCEEW 1.5 billion/year). Brushtail possums (*Trichosurus vulpecula*) still commute along suburban fences at night. Same corridor, opposite needs.

### Non-human stakeholders
- **Primary — possum.** Open runway after dusk.
- **Secondary — small birds at dawn.** Gate must not become a cat sit-spot at first light.
- **Cat.** Not a beneficiary. No ultrasound (it hits wildlife too).

### Project idea
Clamp-on timber/PETG “branch” on a 1.2 m demo fence.

1. `DAY` — gap locked (optional, or park).
2. `DUSK_POSSUM` — lux below threshold; class possum → gap **open**.
3. `DUSK_CAT` — class cat → gap **closed**.
4. `HUMAN` — person on the rail → never pinch (open or ignore).
5. `API_DOWN` → **FAIL OPEN** for possum-sized traffic after dusk.
6. `FAULT` → fail open + maintainer LED.

### One-line architecture
Dusk × body-on-rail × identity → ESP32-S3 → flipper geometry. Justice = fail-open if the image API dies.

```
 WORLD     dusk light          body on rail           who (cat/possum/human)
               │                    │                       │
 SENSE      BH1750              LD2410 or ToF            camera → image API
               │                    │                       │
 FUSE                    ESP32-S3  justice FSM
               │                    │
 ACTUATE              MG90S flipper / gap
 DEMO       stuffed cat vs stuffed possum vs person
```

### Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| BH1750 | I2C | lux | dusk band vs day |
| LD2410 | UART | present, range | 1 s persist; ignore far clutter |
| Camera / HTTP | WiFi | class_id, conf | cat / possum / human / empty |
| End-stop | GPIO 12 | gap_clear | **never close on a body in the gap** |
| FSM | MG90S | open/closed | geometry does most of the work |

### Hardware stack

ESP32-S3-CAM **or** ESP32-S3 + OV2640; BH1750 I2C; LD2410 UART; MG90S; level-shift if needed. Image API (cloud) **or** Edge Impulse on-device as stretch. If cloud: WiFi; if down, fail open.

**Pin overrides.** Camera board steals some default GPIOs — document the silk in `fsm.h`. Keep ADC1 free even if unused. Servo on a pin the CAM board does not use (often GPIO 7 still OK; **verify**).

**Enclosure.** Clamp-on branch: gap width is the design. CAD a possum-body silhouette vs cat-shoulder silhouette. Geometry > classifier.

**BOM delta.** ESP32-S3-CAM (or OV2640), stuffed cat + stuffed possum, 1.2 m timber rail, limit switch in the gap.

**Do not buy.** Ultrasonic “cat deterrent.” Live-cat tests.

### Software stack

`task_lux` · `task_radar` · `task_classify` (HTTP or local, 2–5 s) · `task_fsm` · `task_servo`.

Persist presence **1 s**. `gap_clear` false → freeze servo (pinch-risk). Confidence below 0.6 → treat as `API_DOWN` at dusk.

```
   DAY (gap locked or parked)
        │ lux < L_DUSK
        ▼
   DUSK_WAIT ── class possum / unknown+api_ok==false ──► DUSK_POSSUM (OPEN)
        │
        ├── class cat ──► DUSK_CAT (CLOSED)
        ├── class human ──► HUMAN (never pinch; open if body in gap)
        └── lux > L_DAY ──► DAY

   api_ok false after dusk ──► FAIL_OPEN (possum-sized traffic)
   fault ──► FAIL_OPEN + amber
```

| State | Gap | Rule |
|---|---|---|
| `DAY` | closed | No sit-spot |
| `DUSK_POSSUM` | open | Beneficiary |
| `DUSK_CAT` | closed | Threat |
| `HUMAN` | freeze/open | Safety |
| `FAIL_OPEN` | open | Classifier died; possum must not wait on WiFi |
| `FAULT` | open | Amber |

Write **who pays** for a false positive (closed on a possum) vs false negative (open for a cat). Recommended: false open is the ethical residual; geometry still blocks a bulky cat.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Servo open/close; end-stop aborts close | 3 |
| 8 | HTTP class stub: three JPEGs (cat/possum/person) | 2 |
| 9 | Stuffed possum at dusk-lux → open; stuffed cat → close; unplug WiFi → open | 1+2 |
| 10–11 | Print branch, clamp to rail | 3 |
| 13 | Three bodies + WiFi-down; no live animals | 4 |

### Vision Pro
Eye-height possum vs cat; point at gap “who fits.” Gaze does **not** close the gate.

### Risks
Classifier errors: document who pays. No species-recovery claim. No live-cat tests. NSW still has no statewide cat curfew — the artefact is a fence analog, not law.

---

## 4. Glass-strike shutter

### Challenge statement
Clear glass at dawn reads as sky or habitat. Small woodland birds (silvereyes, spinebills, honeyeaters) strike campus and city panes. Stickers are ignored; the building stays lethal at the hours birds move.

**Literature honesty.** Global cities problem; campus-true; not a listed KTP. Strongest MR overlay in the set.

### Non-human stakeholders
- **Primary — small forest birds.** Need the pane to stop looking like a corridor at civil twilight and in low sun/fog.
- **Secondary — humans in the room.** Shutter must not stay down all day without reason. This is a **model window**.

### Project idea
Table-top framed pane (~600 mm).

1. `DAY_CLEAR` — shutter up.
2. `TWILIGHT_DOWN` — lux band → shutter **down** (default protective).
3. `STRIKE_DOWN` — mmWave bird-sized, fast signature → down even in day.
4. `FOG_DOWNSUN` — weather API → treat as twilight.
5. `HUMAN_OVERRIDE` — optional PIR: do not trap a person in the dark; say this is a model.
6. `FAULT_DOWN` — shutter down (fail safe for birds).

### One-line architecture
Dawn lux + approach + weather → ESP32-S3 → patterned shutter. The window is the UI. Fail-**down**.

```
 WORLD     dawn lux / fog         approach in front of glass
               │                         │
 SENSE      BH1750 + BoM              LD2410  (+ optional cam)
               │                         │
 FUSE                    ESP32-S3
               │                         │
 ACTUATE         MG90S bead-curtain / patterned PETG shutter
 DEMO            darken room + bird silhouette
```

### Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| BH1750 | I2C | lux | civil twilight band |
| LD2410 | UART | present, speed proxy | daytime strike |
| BoM | NVS | fog, low sun, cloud | extra down |
| Optional cam | API | bird-vs-not | Credit; not required for twilight |
| FSM | MG90S (+ optional second) | curtain down/up | pattern **is** the ecology |

### Hardware stack
BH1750, LD2410, MG90S, ESP32-S3, 600 mm frame, laser-cut / printed pattern (dots, not a solid blackout if you want a human-readable “this is for birds” Credit story). Optional camera for bird-vs-not (W8).

**BOM delta.** Acrylic pane, printed shutter with 5–10 cm spacing marks (bird-strike literature: dense pattern), bird silhouette on a stick.

### Software stack

FSM: `UP` `TWILIGHT_DOWN` `STRIKE_DOWN` `FOG_DOWN` `HUMAN_OVERRIDE` `FAULT_DOWN`.

Hysteresis: lux enter twilight at L_LOW, leave at L_HIGH (avoid chatter at a fluorescent room). Strike hold **15 s** after last fast signature.

Fail-**down**. API down → still use lux; do not fail *up*.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Servo drops curtain in <1 s from serial | 3 |
| 8 | Weather JSON fog → down; lux still works if WiFi off | 2 |
| 9 | Darken room → down; wave silhouette in “day” → down (mid-check) | 1+2 |
| 10–12 | Patterned print; AVP bird-view vs human-view | 3+5 |
| 13 | Table-top pane; “not Abercrombie” | 4 |

### Vision Pro
Volumetric flight-corridor into the real pane; gaze toggles bird-view vs human-view. Best MR shot. Headset does not own the servo.

### Risks
Do not claim you retrofitted a campus building. Table-top is a model.

---

## 5. First-flush creek delay

### Challenge statement
Sydney storms flush the dirty first millimetres — oil, sediment, dog faeces — into Cooks, Wolli, Parramatta. Short-finned eels and riparian invertebrates still use these systems. The first flush is the ecological insult, not rain as such. Cite CRVA / EPA if you want the source-first version (idea H in [[from-sources_en]]).

### Non-human stakeholders
- **Primary — eel / macroinvertebrates.** Later, cleaner flow.
- **Secondary — riparian plants.** Not buried in silt every storm.
- Country/water literature; **do not costume** Aboriginal knowledge.

### Project idea
Sandstone-coloured weir, two trays.

**Chosen default:** first N mm always to cistern (simple, demoable).

1. `DRY` — flap to cistern ready (or parked closed).
2. `FIRST_FLUSH` — rain start + high turbidity → dirty cistern.
3. `CLEAN_FLOW` — after N mm **or** turbidity drop → creek tray.
4. `SETTLE` — cistern rests.
5. `PRE_STORM` — BoM nowcast → flap ready.
6. `FAULT` — last safe position (document: cistern).

### One-line architecture
Rain + dirt-in-water + forecast → ESP32-S3 → weir flap. Water is the display.

```
 WORLD     rain                dirt in water              forecast
               │                    │                       │
 SENSE      tip-bucket or         analog turbidity         WiFi BoM
            soil moisture         (LED + photodiode)
               │                    │                       │
 FUSE                    ESP32-S3
 ACTUATE              MG90S weir flap
 DEMO       jug of tea+glitter vs clean water
```

### Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| Tip-bucket **or** soil-moisture analog | GPIO interrupt or ADC1 GPIO 1 | rain_mm, raining | start of storm |
| LED + photodiode across the channel | ADC1 GPIO 2 | turbidity | dirty vs clear |
| BoM nowcast | NVS | rain_next_60m | pre-close |
| FSM | MG90S | cistern vs creek | two trays at 3 m |

### Hardware stack
Rain: tip-bucket **or** analog soil-moisture as proxy. Turbidity: cheap optical analog on **ADC1** (LED 5 V via MOSFET, photodiode into ADC, inverse of light). ESP32-S3, MG90S, two 3D-printed trays, sandstone PETG weir. Electronics dry, above splash.

**BOM delta.** White LED + photodiode, tea + glitter, two trays, optional real tip-bucket.

**Do not.** Hook to campus stormwater. Dye in drains.

### Software stack

States: `DRY` `FIRST_FLUSH` `CLEAN` `SETTLE` `PRE_STORM` `FAULT`.

N_mm demo = 3–5 mm analog (a jug). Turbidity threshold calibrated on tea vs tap. Hysteresis so glitter settling doesn’t chatter the flap.

Fail: flap to **cistern** (fail-protective for the “creek”). API down: still run first-N-mm without nowcast.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Flap dumps jug A to left, jug B to right from serial | 3 |
| 8 | Nowcast stub pre-moves flap | 2 |
| 9 | Tea+glitter → cistern; tap water after → creek (mid-check) | 1+2 |
| 10–11 | Sandstone print, hide the servo in the weir body | 3 |
| 13 | Two jugs at 3 m; Country cited, not costumed | 4 |

### Vision Pro
Dirt particles only in first-flush volume. Scrub one storm. Gaze cistern vs creek.

### Risks
Tiny volumes. No real stormwater hookup. Do not invent a listed eel for a reach you cannot cite.

---

## 6. Noisy-miner clutter baffles

### Challenge statement
Noisy miners (*Manorina melanocephala*) dominate many east-Australian suburban plantings and aggressively exclude small insectivores. Miners prefer park-like openness; wrens and spinebills need clutter.

**Literature honesty.** Real urban-bird ecology; not a 2025–26 national emergency.

### Non-human stakeholders
- **Primary — superb fairywren / eastern spinebill.** Visual clutter.
- **Miner.** Competitor, not a target for noise weapons.

### Project idea
Planter-scale thicket. **No speakers at animals.**

1. `QUIET` — vanes open/park.
2. `MINER_CHORUS` — BirdNET-class ID → vanes close into clutter.
3. `WIND` — BoM/anemometer → don’t fight a gale.
4. `RAIN` — vanes park.
5. `NIGHT` — park.
6. `FAULT` — park (fail to “not damaging itself”).

### One-line architecture
Miner sound + wind/rain/night → ESP32-S3 → shrub vanes. Refuse the speaker. Fail-**park**.

```
 WORLD     miner sound              wind / rain
               │                         │
 SENSE      I2S or analog mic         lux + BoM
               │                         │
 FUSE         ESP32-S3 (+ WiFi to sound API)
 ACTUATE      MG90S shrub vanes ×2–4
 DEMO         closed box + headphones into mic
```

### Hardware stack
INMP441 I2S **or** analog mic + MAX9814 on ADC1 (if WiFi, remember ADC2). ESP32-S3, MG90S ×2–4, laser-cut timber + PETG leaves. If ADC vs WiFi fights: BLE listen-node + actuate-node (Tutorial 6). API: BirdNET-Pi HTTP **or** a campus-safe spectrogram proxy.

**Pin overrides.** I2S: typically GPIO 4 WS, 5 SCK, 6 SD — **then move amber to GPIO 13, pump unused, servo stays 7/11.** Document in the idea’s `sdkconfig`.

**BOM delta.** INMP441 or MAX9814, extra MG90S, closed demo box, headphones.

**Do not.** Campus PA playback (attracts real miners). Anti-miner speakers.

### Software stack

`task_audio` (I2S frames or ADC envelope) · `task_api` (BirdNET) · `task_lux` · `task_fsm` · `task_actuate`.

If using analog-only for W9: energy + peak-rate in miner-ish band as a **proxy**, then W8 API upgrades the label. Report the proxy as a proxy.

Fail-**park**. Wind > threshold parks even during chorus.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Four vanes close from serial | 3 |
| 8 | File upload / HTTP “miner” vs “wind” | 2 |
| 9 | Headphones in a **box** close vanes; silence parks | 1+2 |
| 13 | Planter reads as a bush from 3 m | 4 |

### Vision Pro
Acoustic field as volume; gaze on the small-bird pocket.

### Risks
Attracting real miners with playback — don’t. Wrens do not owe you a gesture set.

---

## 7. Honeyeater bath that is not a cat bowl

### Challenge statement
Heatwave bird baths are one of the few water sources left in suburbs. They also create a cat hunting perch. Two Australian issues (heat; outdoor cats) meet in one bowl.

### Non-human stakeholders
- **Primary — New Holland honeyeater / little wattlebird.** Cool, shallow water in heat.
- **Cat.** Not a beneficiary. No live-cat tests.

### Project idea
Stone-look bowl, thin pedestal (hard to sit on).

1. `OPEN_HEAT` — water temp OK, day, no cat → open.
2. `RECIRC` — water hot → pump / shade.
3. `LID_CAT` — mmWave cat-sized on rim + dusk → **lid / raise**.
4. `BIRD_HOLD` — image bird → stay open.
5. `LID_NIGHT` — lid (mosquitoes + cats).
6. `EMPTY` / `FAULT` — maintainer: dump water (manual); pump off.

Write **two** ESD tables (bird drink vs cat hunt).

### One-line architecture
Water temp × dusk × who-on-rim → ESP32-S3 → lid + recirculation. The lid is a tangible vote for the bird.

```
 WORLD     water temp         dusk          who on the rim
               │                │                 │
 SENSE      DS18B20          BH1750         mmWave + image API
 FUSE                    ESP32-S3
 ACTUATE    5 V pump          MG90S lid
 DEMO       hot vs cool water; stuffed cat on rim
```

### Hardware stack
DS18B20 in a **thermal well** (not live mains water). BH1750, LD2410 at rim height, optional camera, pump, MG90S lid, ESP32-S3. Electronics in the pedestal, IP-ish gasket, never in the bowl.

**BOM delta.** Stone-look print, pump, stuffed cat, shallow dish (empty nightly).

### Software stack

States: `OPEN_HEAT` `RECIRC` `LID_CAT` `LID_NIGHT` `BIRD_HOLD` `FAULT`.

Lid must not close on a detected body in the bowl (pinch). Night default lid. Heatwave BoM can **pre-fill** analog (pump into bowl) before 10:00 — fail-**off** if tank empty.

Conflicting desired: if bird **and** cat signatures, **lid** (do not build a cat table). Document that the bird loses that minute; geometry (thin pedestal) is the rest of the defence.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Lid + pump PWM | 3 |
| 8 | Image stub cat vs bird; BoM heat pre-fill | 2 |
| 9 | Hot water → recirc; stuffed cat at dusk → lid | 1+2 |
| 13 | Empty-and-clean in the manual (mosquitoes) | 4 |

### Vision Pro
Cat cone vs bird cone. Gaze = water false-colour.

### Risks
Mosquitoes: empty-and-clean. No live cats. Standing water ethics in the report.

---

## 8. Harbour silt valve (oyster tray)

### Challenge statement
After rain, Sydney Harbour gets a freshwater/silt pulse. Sydney rock oysters (*Saccostrea glomerata*) cannot feed well in that window. Estuarine aquaculture and wild beds share the insult.

### Non-human stakeholders
- **Primary — oyster.** Less silt on the feeding window.
- **Secondary — seagrass (light climate).** Mention in the essay; do not over-claim the tank. If you want the 2026 *Posidonia* story, see source-first idea E — do not run two tank projects.

### Project idea
10 L tank, mini timber rack. **Electronics never in salt water** — freshwater analog with silt.

1. `CLEAR` — flap open / bubbles off.
2. `SILT` — turbidity up → close the rack analog.
3. `PRE_STORM` — BoM → pre-close.
4. `REOPEN` — turbidity down → open.
5. `TEMP` — optional shock.
6. `FAULT` — rack closed.

### One-line architecture
Silt water + rain forecast → ESP32-S3 → flap (and/or air). One calm valve in a system that is already pervasive (the harbour). Fail-**closed** (protective).

```
 WORLD     silt water              rain forecast
               │                         │
 SENSE      analog turbidity + DS18B20    WiFi BoM
 FUSE                    ESP32-S3
 ACTUATE    MG90S flap  and/or  air pump
 DEMO       pour silt water; valve closes
```

### Hardware stack
Optical turbidity on ADC1, DS18B20 in a dry-well or sealed sleeve, ESP32-S3, MG90S, optional 5 V air pump, timber+PETG rack. Isolate 3.3 V from wet side. Tank on a tray.

**BOM delta.** 10 L tank, potting silt / bentonite, timber rack. **No salt.**

### Software stack

States: `CLEAR` `SILT` `PRE_STORM` `REOPEN` `TEMP` `FAULT`.

Hold `SILT` 30 s after turbidity drops (hysteresis). API down: still close on turbidity; skip pre-close.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Flap closes from serial; electronics stay dry | 3 |
| 8 | BoM rain → pre-close | 2 |
| 9 | Pour silt → close; flush clear → open (readable at 3 m) | 1+2 |
| 13 | “Not a harbour deployment” | 4 |

### Vision Pro
Silt volume registered to the rack. MR shows filtering; motors do the analog.

### Risks
Salt kills PCBs. No harbour deployment. Tiny tank ≠ estuary.

---

## 9. Bell-frog hydroperiod pump

### Challenge statement
Green and golden bell frogs (*Litoria aurea*, endangered) need ponds with a **hydroperiod**: wet long enough to metamorphose, not a permanent fish/mosquito slough, and not a flush that washes eggs. Sydney Olympic Park is the famous site. **Demo:** striped marsh frog / common eastern froglet calls + a tray; name *L. aurea* as the stakeholder and say the analog is not a listed-species pond.

### Non-human stakeholders
- **Primary — tadpoles of the named species.** Hydroperiod.
- **Secondary — adults.** Calling habitat after rain, without a pump that drowns rafts.

### Project idea
Ceramic/PETG “rock” at a 15 L tray. Product is the hydroperiod, not a fountain.

1. `DRY` — soil dry, no rain → **do not fill**.
2. `FILL` — rain + forecast of following dry heat → fill to tadpole depth.
3. `HOLD` — maintain band for N days (demo: N minutes).
4. `REFUSE_FLOOD` — too full → refuse pump; maybe drain a little.
5. `RECIRC` — optional tiny recirculation, **not** fish.
6. `FAULT_OFF` — pump off.

### One-line architecture
Rain + water level + forecast → ESP32-S3 → pump that can **refuse**. Fail-**off**.

```
 WORLD     rain / soil            water depth           frog call (optional)
               │                      │                      │
 SENSE      moisture or           ultrasonic ToF          analog mic
            tip-bucket            (level)
 FUSE                    ESP32-S3 + BoM
 ACTUATE    5 V pump + MOSFET     optional drain servo
 DEMO       pour “rain”; show refuse-to-overfill
```

### Hardware stack
Soil moisture analog **or** tip-bucket; **ToF** water level (HC-SR04 acoustics vs “frogs” is a bad joke in the report — VL53L0X / waterproof ToF better); DHT22; ESP32-S3; pump; optional I2S mic + frog API as Credit. Overflow notch is mechanical (justice in the plastic).

**Pins.** ToF I2C shares 8/9 with lux if you also use BH1750 — use a mux or skip lux. Level on I2C; moisture on ADC1 GPIO 1.

**BOM delta.** 15 L tray, pump, ToF, soil-moisture probe, overflow spout.

### Software stack

States: `DRY` `FILL` `HOLD` `REFUSE_FLOOD` `RECIRC` `FAULT_OFF`.

Level band e.g. 40–60 mm analog. `REFUSE_FLOOD` if level > 70 mm **or** rain continues past N. BoM heat after rain → allow `FILL`; BoM week of rain → refuse (flush analog).

Optional mic is **not** a tadpole detector; it is Credit context (“adults calling after rain”).

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Pump fills to a mark then **stops**; overflow notch works even if firmware dies | 1+3 |
| 8 | BoM: dry-heat-after-rain allows fill; long-rain refuses | 2 |
| 9 | Pour rain → fill; keep pouring → refuse (mid-check) | all |
| 13 | Honesty slide: *L. aurea* vs marsh-frog analog; no live tadpoles | 4 |

### Vision Pro
Water-volume over time as a ghost in the tray (hydroperiod you cannot see in a snapshot).

### Risks
Animal ethics: no live tadpoles. No dye in campus drains. Honesty about *L. aurea* vs demo frog.

---

## 10. Bandicoot dawn underpass

### Challenge statement
Long-nosed bandicoots (*Perameles nasuta*) still persist at Sydney remnant edges (Centennial Park, North Head and others). Fences, cats, foxes, and dawn pedestrians cut **ground** runways. This is not the fence-top possum problem: it is a snuffle-height tunnel.

### Non-human stakeholders
- **Primary — bandicoot.** Dark, cat-excluding crawl at dawn/dusk.
- **Secondary — cats/foxes.** Excluded by geometry + lid, not ultrasound.

### Project idea
Timber/PETG under-fence tunnel on a demo panel.

1. `DAY_SHUT` — optional closed.
2. `DUSK_OPEN` — lux in dawn/dusk band, no cat-class body → tunnel open.
3. `CAT_SHUT` — image/mmWave cat → flap **closed**.
4. `PREY_OPEN` — low, long body class → open.
5. `FAIL_DUSK_OPEN` — API down: fail **open** at lux-dusk, closed in bright day.
6. `FAULT` — maintainer LED.

### One-line architecture
Same family as idea 3, **different enclosure and height**. Geometry does most of the work. Ground-level proxemics.

```
 WORLD     dawn lux             body in tunnel mouth         class
               │                      │                      │
 SENSE      BH1750                LD2410 / ToF            camera API
 FUSE                    ESP32-S3
 ACTUATE              MG90S flap  (CAD cross-section is the product)
 DEMO       stuffed bandicoot vs stuffed cat vs shoe
```

### Hardware stack
BH1750, LD2410, camera, MG90S, ESP32-S3. Tunnel cross-section is the real design: bandicoot fits, cat shoulders do not. Flap is a backup, not the whole story.

**CAD.** Internal H×W from published *P. nasuta* body measurements; cat exclusion from TAP-style “cat cannot pass” diagrams — cite, don’t guess. Snuffle-height camera analog is a peephole, not a UI.

**BOM delta.** Same as idea 3 plus a 1.5 m timber panel and stuffed bandicoot (or 3D-printed body analog).

### Software stack

FSM: `DAY_SHUT` `DUSK_OPEN` `CAT_SHUT` `PREY_OPEN` `FAIL_DUSK_OPEN` `FAULT`.

Recommended fail: **open at lux-dusk, closed in bright day** if API dies. Never pinch: ToF in the throat.

Do not copy idea 3’s fence-top essay. Write ground proxemics (Vogel & Balakrishnan at 15 cm height).

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Flap + geometry mock in cardboard | 3 |
| 8 | Classifier stuffed-cat vs low body | 2 |
| 9 | Dawn-lux open; cat-class shut; WiFi down at dusk → open | 1+2 |
| 13 | Crouch the assessor; no live bandicoots | 4 |

### Vision Pro
Snuffle-height camera (crouch overlay). Gaze does not slam the flap.

### Risks
Do not test on live bandicoots. Geometry > classifier. Do not claim you fixed Centennial Park.

---

**11–20 packs:** [[packs_11-20_en]]
