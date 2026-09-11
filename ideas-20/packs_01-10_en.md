---
course: COMP4447 / COMP5047
title: "Ideas 1–10 — proposal + architecture packs (EN)"
updated: 2026-09-12
lang: en
aliases:
  - packs 1-10 EN
---

# Proposal + architecture packs — ideas 1–10

> **中文**：[[packs_01-10_cn]] · **Index (challenge + IxD)**：[[00_index_en]] · **11–20**：[[packs_11-20_en]]
> Same skeleton as the Wildlife-First bollard: Challenge → Non-human stakeholders → Behaviours → Architecture map → Hardware → Software FSM → Parts → Timeline → Risks → AVP (not Pass).
> **Team table:** copy from [[00_index_en]] / `TEAM.md`. Tick exactly one PM. Five MCS students; extra people do not shrink the artefact.
> **Calendar now:** Week 6 done. Tutor must approve a pivot (proposal was due W4). W7 motors · W8 APIs · W9 mid-check (one feature, no headset) · W10–12 integrate + optional visionOS · W13 showcase.
> **Shared five-person split:** (1) sensors/power (2) ESP-IDF FSM (3) CAD + actuators (4) evidence/docs/video (5) visionOS after W9.

---

## 1. Camp-edge heat refuge

### Challenge statement
Grey-headed flying-foxes (*Pteropus poliocephalus*, EPBC vulnerable) roost in Sydney camps. Extreme heat events at and above ~42 °C cause mass deaths, especially of pups and lactating females (NSW heat-stress guidance; Welbergen-type literature). Darkness is not the issue here: **heat and shade** are. We do **not** install in a live camp this semester.

### Non-human stakeholders
- **Primary — pups / dependents.** Need evaporative cooling and shade when thresholds are crossed.
- **Secondary — adults.** Need an undisturbed camp; our device must not become a tourist mister.
- **Tertiary — roost trees.** Need not to be soaked into rot; mist is short, directed, default off.

### Project idea (behaviours)
Hanging PETG “leaf” in a tree analog (heat-box on campus).
1. `idle` — sensors on, mist off, leaf open to ambient.
2. `forecast_heat` — BoM says peak will cross threshold → leaf folds to shade **before** the peak.
3. `live_heat` — DHT22 + IR canopy temp in-band → mist PWM on, short bursts.
4. `recover` — temps fall → mist off, leaf returns.
5. `fault` — tank empty / pump stall → underside LED for a **human** maintainer only.
6. `inhibit_day_cool` — below threshold, do nothing.

### Architecture map
```
WORLD     air heat/RH          canopy surface         forecast / clock
              │                      │                      │
SENSE      DHT22                 MLX90614 or lux         WiFi → BoM
              │                      │                      │
FUSE                    ESP32-S3 / ESP-IDF  six-state
              │                      │                      │
ACTUATE    MOSFET + pump         MG90S leaf-shade      maintainer LED
POWER      USB bench / 18650     water tank
DEMO       heat gun / hot box    never a live camp
```

### Hardware stack
| Layer | Parts | Bus |
|---|---|---|
| Enclosure | Fusion hanging leaf, bark-coloured PETG, tank pocket | — |
| Compute | ESP32-S3 | — |
| Sense | DHT22 (temp/RH); MLX90614 **or** BH1750 | 1-wire/I2C |
| Actuate | 5 V pump + MOSFET; MG90S; underside amber | PWM / GPIO |
| Net | WiFi STA | BoM / Open-Meteo |

**Pins (contract).** I2C GPIO 8/9; DHT GPIO 4; pump MOSFET GPIO 6; servo GPIO 7; amber GPIO 5. Analog sensors on ADC1 (Tutorial 6: WiFi kills ADC2).

### Software stack
Tasks: `task_climate` 1 s · `task_ir` 500 ms · `task_forecast` 10 min · `task_fsm` 50 ms · `task_actuate` 20 ms. Policy only in FSM. Cache forecast in NVS if radio drops → fail **off** (do not mist blind).

### Parts / enclosure / APIs
ESP32-S3, DHT22, MLX90614 or BH1750, MG90S, 5 V pump, tank, MOSFET, PETG. **API:** BoM. ALA/iNaturalist = evidence, not runtime.

### Vision Pro (second station)
Thermal volume around the physical leaf; pinch to scrub 6 h forecast; gaze-dwell “pup vs adult.” **Must not fire the mist.**

### Timeline / risks
W7 pump+servo · W8 BoM · W9 one threshold demo · W10–11 print leaf · W12 overlay · W13 heat-box. **Risks:** ethics/NPWS if anyone proposes a real camp (refuse); pump leak; DHT in sun reads high (shade the probe). Do not claim population recovery.

---

## 2. Brush-turkey mound kiln

### Challenge statement
Australian brush-turkeys (*Alectura lathami*) incubate eggs by farming mound temperature around **33 °C**. Suburban Sydney now hosts them; people rake mounds as “mess,” killing the clutch. The bird already has an interface. We join it.

### Non-human stakeholders
- **Primary — eggs/embryos.** Stable incubation band.
- **Secondary — the male.** He is the other operator; our vents must not fight him into abandoning the analog.
- **Tertiary — humans with rakes.** Need a readable “do not rake” flag, not a notification.

### Project idea
Mulch-filled PETG mound analog + aquarium heater for demo.
1. `too_cool` — vents closed, optional small heater **in the analog only**.
2. `in_band` (~32–34 °C) — vents idle; **flag up**.
3. `too_hot` — vents/fan open.
4. `rain_front` — BoM cold rain → do not open vents wide.
5. `fault` — probe missing → flag down, no false “safe to rake.”
6. `demo_idle` — heater off for transport.

### Architecture map
```
WORLD     mound core temp         air / rain forecast
              │                         │
SENSE      DS18B20 ×2                DHT22 + WiFi BoM
              │                         │
FUSE              ESP32-S3  band FSM
              │                         │
ACTUATE    MG90S vents + 5 V fan     servo flag
DEMO       sandbox + aquarium heater  28 / 33 / 38 °C
```

### Hardware stack
DS18B20 probes (waterproof) on 1-wire; DHT22; ESP32-S3; MG90S ×2 (vent + flag); 5 V fan + MOSFET; PETG thatched shell. **Do not** bury electronics in a real mound.

### Software stack
`task_probe` 1 s (median of two DS18B20) · `task_air` · `task_bom` · `task_fsm` · `task_actuate`. Hysteresis ±0.5 °C so vents do not chatter. States: `TOO_COOL` `IN_BAND` `TOO_HOT` `WEATHER_HOLD` `PROBE_FAULT` `IDLE`.

### Vision Pro
33 °C isotherm volume inside the physical mound; two-hand egg-depth; gaze on flag = “raking now kills the clutch.”

### Timeline / risks
Most Week-13-proof artefact. Risk: students “help” with a heater in a way the report over-claims. Say clearly: analog, not a real nest.

---

## 3. Dusk cat-gate for possums

### Challenge statement
Outdoor cats (pets and ferals) are a leading driver of Australian mammal and bird decline (Woinarski / Legge literature). Brushtail possums (*Trichosurus vulpecula*) still commute along suburban fences at night. Same corridor, opposite needs.

### Non-human stakeholders
- **Primary — possum.** Open runway after dusk.
- **Secondary — small birds at dawn.** Gate must not become a cat sit-spot at first light.
- **Cat.** Not a beneficiary; we do not use ultrasound (it hits wildlife too).

### Project idea
Clamp-on timber/PETG “branch” on a 1.2 m demo fence.
1. `day` — gap locked (optional).
2. `dusk_possum` — BH1750 below threshold; mmWave + image says possum → gap **open**.
3. `dusk_cat` — image says cat → gap **closed**.
4. `human` — person on the rail → open or ignore, never pinch.
5. `api_down` → **FAIL OPEN** for possum-sized traffic.
6. `fault` → fail open + maintainer LED.

### Architecture map
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

### Hardware stack
ESP32-S3 with camera (S3-CAM) **or** ESP32-S3 + OV2640; BH1750 I2C; LD2410 UART; MG90S; level-shift if needed. Image API (cloud) **or** Edge Impulse on-device as stretch. If cloud: WiFi; if down, fail open.

### Software stack
`task_lux` · `task_radar` · `task_classify` (HTTP or local) · `task_fsm` · `task_servo`. Persistence 1 s. Never close on a body already in the gap (pinch-risk).

### Vision Pro
Eye-height overlay possum vs cat; point at gap “who fits.” Gaze does **not** close the gate.

### Timeline / risks
Classifier errors: document who pays. No species-recovery claim. No live-cat tests.

---

## 4. Glass-strike shutter

### Challenge statement
Clear glass at dawn reads as sky or habitat. Small woodland birds (silvereyes, spinebills, honeyeaters) strike campus and city panes. Stickers are ignored; the building stays lethal at the hours birds move.

### Non-human stakeholders
- **Primary — small forest birds.** Need the pane to stop looking like a corridor at civil twilight and in low sun/fog.
- **Secondary — humans in the room.** Need the shutter not to stay down all day without reason.

### Project idea
Table-top framed pane (600 mm).
1. `day_clear` — shutter up.
2. `civil_twilight` — lux band → shutter **down** (default protective).
3. `approach` — mmWave bird-sized, fast signature → shutter down even in day.
4. `fog_lowsun` — weather API → treat as twilight.
5. `human_in_room` — optional PIR: do not trap a person in the dark; this is a **model window**, say so.
6. `fault` — shutter down (fail safe for birds).

### Architecture map
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

### Hardware / software
BH1750, LD2410, MG90S, ESP32-S3, optional camera for bird-vs-not (W8). FSM: `UP` `TWILIGHT_DOWN` `STRIKE_DOWN` `FOG_DOWN` `HUMAN_OVERRIDE` `FAULT_DOWN`. Fail **down**.

### Vision Pro
Volumetric flight-corridor into the real pane; gaze toggles bird-view vs human-view. Best MR shot. Headset does not own the servo.

### Risks
Do not claim you retrofitted Abercrombie. Table-top is a model.

---

## 5. First-flush creek delay

### Challenge statement
Sydney storms flush the dirty first millimetres — oil, sediment, dog faeces — into Cooks, Wolli, Parramatta. Short-finned eels and riparian invertebrates still use these systems. The first flush is the ecological insult, not rain as such.

### Non-human stakeholders
- **Primary — eel / macroinvertebrates.** Need later, cleaner flow.
- **Secondary — riparian plants.** Need not to be buried in silt every storm.
- Cite Country/water literature; **do not costume** Aboriginal knowledge.

### Project idea
Sandstone-coloured weir, two trays.
1. `dry` — flap closed to cistern? or open to creek — pick one and stick.
2. `first_flush` — rain start + high turbidity → dirty cistern.
3. `clean_flow` — after N mm or turbidity drop → creek tray.
4. `settle` — cistern rests.
5. `pre_close` — BoM nowcast → flap ready.
6. `fault` — leave flap in last safe position; document it.

**Chosen default:** first N mm always to cistern (simple, demoable).

### Architecture map
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

### Hardware / software
Rain: tip-bucket **or** analog soil-moisture as proxy. Turbidity: cheap optical analog on ADC1. ESP32-S3, MG90S, two 3D-printed trays. States: `DRY` `FIRST_FLUSH` `CLEAN` `SETTLE` `PRE_STORM` `FAULT`.

### Vision Pro
Dirt particles only in first-flush volume. Scrub one storm. Gaze cistern vs creek.

### Risks
Tiny volumes. No real stormwater hookup.

---

## 6. Noisy-miner clutter baffles

### Challenge statement
Noisy miners (*Manorina melanocephala*) dominate many east-Australian suburban plantings and aggressively exclude small insectivores (Grey et al. and subsequent urban-bird work). Miners prefer park-like openness; wrens and spinebills need clutter.

### Non-human stakeholders
- **Primary — superb fairywren / eastern spinebill.** Visual clutter.
- **Miner.** Competitor, not a target for noise weapons.

### Project idea
Planter-scale thicket. **No speakers at animals.**
1. `quiet` — vanes open/park.
2. `miner_chorus` — BirdNET-class ID → vanes close into clutter.
3. `wind` — BoM/anemometer → don’t fight a gale.
4. `rain` — vanes park.
5. `night` — park.
6. `fault` — park (fail to “not damaging itself”).

### Architecture map
```
WORLD     miner sound              wind / rain
              │                         │
SENSE      I2S or analog mic         lux + BoM
              │                         │
FUSE         ESP32-S3 (+ WiFi to sound API)
ACTUATE      MG90S shrub vanes
DEMO         closed box + headphones into mic
```

### Hardware / software
INMP441 I2S **or** analog mic + amp on ADC1 (if WiFi, remember ADC2). ESP32-S3, MG90S ×2–4, laser-cut timber + PETG leaves. If ADC vs WiFi fights: BLE listen-node + actuate-node (Tutorial 6). API: BirdNET-style. Playback **into a box**, never campus PA.

### Vision Pro
Acoustic field as volume; gaze on the small-bird pocket.

### Risks
Attracting real miners with playback — don’t.

---

## 7. Honeyeater bath that is not a cat bowl

### Challenge statement
Heatwave bird baths are one of the few water sources left in suburbs. They also create a cat hunting perch. Two Australian issues (heat; outdoor cats) meet in one bowl.

### Non-human stakeholders
- **Primary — New Holland honeyeater / little wattlebird.** Cool, shallow water in heat.
- **Cat.** Not a beneficiary. No live-cat tests.

### Project idea
Stone-look bowl, thin pedestal.
1. `heat_open` — water temp OK, day, no cat → open.
2. `water_hot` — recirculate / shade.
3. `cat_dusk` — mmWave cat-sized on rim + lux dusk → **lid / raise**.
4. `bird` — image bird → stay open.
5. `night_default` — lid (mosquitoes + cats).
6. `empty` — maintainer: dump water (manual).

### Architecture map
```
WORLD     water temp         dusk          who on the rim
              │                │                 │
SENSE      DS18B20          BH1750         mmWave + image API
FUSE                    ESP32-S3
ACTUATE    5 V pump          MG90S lid
DEMO       hot vs cool water; stuffed cat on rim
```

### Hardware / software
DS18B20 in a thermal well (not live mains water). BH1750, LD2410, optional camera, pump, MG90S lid, ESP32-S3. States: `OPEN_HEAT` `RECIRC` `LID_CAT` `LID_NIGHT` `BIRD_HOLD` `FAULT`. Write **two** ESD tables in the report.

### Vision Pro
Cat cone vs bird cone. Gaze = water false-colour.

### Risks
Mosquitoes: empty-and-clean in the manual. No live cats.

---

## 8. Harbour silt valve (oyster tray)

### Challenge statement
After rain, Sydney Harbour gets a freshwater/silt pulse. Sydney rock oysters (*Saccostrea glomerata*) cannot feed well in that window. Estuarine aquaculture and wild beds share the insult.

### Non-human stakeholders
- **Primary — oyster.** Less silt on the feeding window.
- **Secondary — seagrass (light climate).** Mention in the essay; do not over-claim the tank.

### Project idea
10 L tank, mini timber rack. **Electronics never in salt water** — sensors in sleeves / freshwater analog with silt.
1. `clear` — flap open / bubbles off.
2. `silt_spike` — turbidity up → flap “close the rack” analog.
3. `storm_coming` — BoM → pre-close.
4. `clearing` — turbidity down → open.
5. `temp_shock` — optional.
6. `fault` — rack closed.

### Architecture map
```
WORLD     silt water              rain forecast
              │                         │
SENSE      analog turbidity + DS18B20    WiFi BoM
FUSE                    ESP32-S3
ACTUATE    MG90S flap  and/or  air pump
DEMO       pour silt water; valve closes
```

### Hardware / software
Optical turbidity on ADC1, DS18B20, ESP32-S3, MG90S, optional 5 V air pump, timber+PETG rack. Isolate 3.3 V from wet side. States: `CLEAR` `SILT` `PRE_STORM` `REOPEN` `TEMP` `FAULT`.

### Vision Pro
Silt volume registered to the rack.

### Risks
Salt kills PCBs. No harbour deployment.

---

## 9. Bell-frog hydroperiod pump

### Challenge statement
Green and golden bell frogs (*Litoria aurea*, endangered) need ponds with a **hydroperiod**: wet long enough to metamorphose, not a permanent fish/mosquito slough, and not a flush that washes eggs. Sydney Olympic Park is the famous site. **Demo:** striped marsh frog / common eastern froglet calls + a tray; name *L. aurea* as the stakeholder and say the analog is not a listed-species pond.

### Non-human stakeholders
- **Primary — tadpoles of the named species.** Hydroperiod.
- **Secondary — adults.** Calling habitat after rain, without a pump that drowns rafts.

### Project idea
Ceramic/PETG “rock” at a 15 L tray.
1. `dry_wait` — soil dry, no rain → do not fill.
2. `rain_pulse` — rain + forecast of following dry heat → fill to tadpole depth.
3. `hold` — maintain band for N days (demo: N minutes).
4. `too_full / flush` — refuse pump; maybe drain a little.
5. `mosquito_stagnant` — optional: tiny recirculation, **not** fish.
6. `fault` — pump off.

### Architecture map
```
WORLD     rain / soil            water depth           frog call (optional)
              │                      │                      │
SENSE      moisture or           ultrasonic ToF          analog mic
           tip-bucket            (level)
FUSE                    ESP32-S3 + BoM
ACTUATE    5 V pump + MOSFET     optional drain servo
DEMO       pour “rain”; show refuse-to-overfill
```

### Hardware / software
Soil moisture analog **or** tip-bucket; HC-SR04 or ToF water level (mind acoustics vs frogs — ToF better); DHT22; ESP32-S3; pump; optional I2S mic + BirdNET-frog as Credit API. States: `DRY` `FILL` `HOLD` `REFUSE_FLOOD` `RECIRC` `FAULT_OFF`.

### Vision Pro
Water-volume over time as a ghost in the tray (hydroperiod you cannot see in a snapshot).

### Risks
Animal ethics: no live tadpoles required. Do not release water into campus drains with dye. Honesty about *L. aurea* vs demo frog.

---

## 10. Bandicoot dawn underpass

### Challenge statement
Long-nosed bandicoots (*Perameles nasuta*) still persist at Sydney remnant edges (Centennial Park, North Head and others). Fences, cats, foxes, and dawn pedestrians cut **ground** runways. This is not the fence-top possum problem: it is a snuffle-height tunnel.

### Non-human stakeholders
- **Primary — bandicoot.** Dark, cat-excluding crawl at dawn/dusk.
- **Secondary — cats/foxes.** Excluded by geometry + lid, not ultrasound.

### Project idea
Timber/PETG under-fence tunnel on a demo panel.
1. `day` — optional closed.
2. `crepuscular_open` — lux in dawn/dusk band, no cat-class body → tunnel open.
3. `cat` — image/mmWave cat → flap **closed**.
4. `bandicoot` — low, long body class → open.
5. `api_down` → fail **open** at snuffle hours, closed at noon? **Pick and justify.** Recommended: fail open at lux-dusk, closed in bright day.
6. `fault` — maintainer LED.

### Architecture map
```
WORLD     dawn lux             body in tunnel mouth         class
              │                      │                      │
SENSE      BH1750                LD2410 / ToF            camera API
FUSE                    ESP32-S3
ACTUATE              MG90S flap  (geometry does most of the work)
DEMO       stuffed bandicoot vs stuffed cat vs shoe
```

### Hardware / software
Same family as idea 3, **different enclosure and height**. BH1750, LD2410, camera, MG90S, ESP32-S3. Tunnel cross-section is the real design (CAD). FSM: `DAY_SHUT` `DUSK_OPEN` `CAT_SHUT` `PREY_OPEN` `FAIL_DUSK_OPEN` `FAULT`.

### Vision Pro
Snuffle-height camera (crouch overlay). Gaze does not slam the flap.

### Risks
Do not test on live bandicoots. Geometry > classifier.

---

**11–20 packs:** [[packs_11-20_en]]
