---
course: COMP4447 / COMP5047
title: "20 ideas — proposal + architecture packs (EN index)"
updated: 2026-09-12
lang: en
aliases:
  - packs EN
  - packs_en
---

# Proposal + architecture packs — all 20 ideas (EN)

> **中文**：[[packs_cn]] · **Challenge + IxD index**：[[00_index_en]]
> **Read first：** [[architecture_shared_en]] (MCU, pins, firmware layers, fail-mode grammar, week plan, shared cart).

| Pack | Ideas |
|---|---|
| [[packs_01-10_en]] | 1 camp-edge heat · 2 brush-turkey kiln · 3 dusk cat-gate · 4 glass-strike · 5 first-flush · 6 miner baffles · 7 honeyeater bath · 8 harbour silt · 9 bell-frog hydroperiod · 10 bandicoot underpass |
| [[packs_11-20_en]] | 11 fruit-net slack · 12 stingless-bee door · 13 seedling drip · 14 Phytophthora boot-gate · 15 blue-tongue bait · 16 penguin shore-gate · 17 sugar-glider crossing · 18 water-dragon rock · 19 oystercatcher rope · 20 echidna mow-stake |

Team table: copy from [[00_index_en]] / `TEAM.md`. Tick exactly one PM.

**Calendar now:** Week 6 done. Tutor must approve a pivot (proposal was due W4). W7 motors · W8 APIs · W9 mid-check (one feature, no headset) · W10–12 integrate + optional visionOS · W13 showcase.

Source-first shortlist (if Distinction literature is the priority): [[from-sources_en]].

---

## All 20 at a glance (hardware / software / fail)

Shared MCU = ESP32-S3, ESP-IDF, C. Shared dusk/presence parts = BH1750 + LD2410 unless noted. Full pin contract: [[architecture_shared_en]].

| # | Sense (≥2) | Actuate | Fail | W8 API | W9 mid-check |
|---|---|---|---|---|---|
| 1 | DHT22 + IR canopy | pump + leaf servo | **off** | BoM heat | heat-box: shade before mist |
| 2 | DS18B20 ×2 + DHT | vents + flag | park; flag down if probe dies | BoM rain | 28/33/38 °C flag only in-band |
| 3 | lux + mmWave + cam | gap servo | **open** (possum) | image class | stuffed cat vs possum; WiFi-down open |
| 4 | lux + mmWave | shutter | **down** | weather / bird | darken room + silhouette |
| 5 | rain + turbidity | weir flap | to cistern | BoM nowcast | tea+glitter vs tap |
| 6 | mic + lux | shrub vanes | park | BirdNET | headphones in a **box** |
| 7 | water T + lux + mmWave | lid + pump | lid on cat; pump off if empty | image + BoM | hot water recirc; stuffed cat lid |
| 8 | turbidity + T | rack flap | **closed** | BoM rain | pour silt / flush clear |
| 9 | moisture + level | pump (can refuse) | **off** | BoM hydroperiod | pour rain then refuse overfill |
| 10 | lux + mmWave + cam | tunnel flap | dusk **open** | image class | geometry + stuffed cat |
| 11 | load + lux + beam | pawl (spring slacks) | **slack** | BoM wind | sandbag at night-lux |
| 12 | hive T ×2 + rain | entrance slider | park (dummy hive) | BoM max T | heat gun + 5 min hysteresis |
| 13 | soil + air T | drip | **off** | BoM heat | two pots: wet refuse / dry drip |
| 14 | turbidity/TDS + presence | latch | **closed** (demo) | BoM storm bypass | dirty vs rinsed boot |
| 15 | mmWave + lux | bait cover | **closed** | image skink | day lock; stuffed skink |
| 16 | lux + height/gait | tall flap; fixed slot | slot open / tall shut | image class | low-slow vs tall-fast |
| 17 | lux + wind + occupancy | reel | don’t extend if no wind sense | BoM wind | fan retract; toy on line holds |
| 18 | range + IR on rock | slow frond | **park**; freeze if occupied | optional pose | toy on rock: no jerk |
| 19 | tide tray + mmWave | rope winch | **up** at high tide | tide JSON | fill tray → rope; no siren |
| 20 | vibration + lux + mmWave | flag | **up** | image mower | three taps; toy mower |

## How to implement (any idea)

1. Read [[architecture_shared_en]] and order the shared cart **plus** that idea’s BOM delta (two of every demo-critical module).
2. Clone the firmware layout; put **all policy in `fsm.c`**. Drivers do not start pumps.
3. Week 7: actuator moves from a dummy flag. Week 8: API cached in NVS with a radio-down path. Week 9: one feature **without** a headset.
4. Write ESD for the **non-human** (two tables if desired states conflict: 3, 7, 15, 16).
5. visionOS only after W9, registered to the object, never firing motors.
6. Week 13 script is in each pack: campus analog, stuffed/playback/jug, one honest sentence about what it is **not**.

Pick using the table in [[00_index_en]] (MR / artefact-if-headset-slips / proxemics / Country / literature). If Distinction citations matter more than IxD charm, start from [[from-sources_en]] and map onto the nearest row above (cats → 3/10/16; flying-fox heat → 1; first-flush → 5; Phytophthora → 14).
