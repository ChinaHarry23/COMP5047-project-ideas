---
course: COMP4447 / COMP5047
title: "Ideas 11–20 — detailed proposal + hardware/software architecture (EN)"
updated: 2026-09-12
lang: en
aliases:
  - packs 11-20 EN
---

# Proposal + architecture packs — ideas 11–20 (detailed)

> **中文**：[[packs_11-20_cn]] · **Index**：[[00_index_en]] · **1–10**：[[packs_01-10_en]] · **Shared contract**：[[architecture_shared_en]]
> Same skeleton as 1–10. Pins, power, firmware layers, fail-mode grammar: [[architecture_shared_en]].
> **Team:** copy from [[00_index_en]] / `TEAM.md`. Tick exactly one PM.

**Shared five-person split:** (1) sensors/power (2) ESP-IDF FSM (3) CAD + actuators (4) evidence/docs/video (5) visionOS after W9.

---

## 11. Wildlife-friendly fruit-net slack

### Challenge statement
Backyard fruit nets kill grey-headed flying-foxes when mesh and tension turn the tree into a trap. WIRES / DCCEEW “wildlife-friendly netting” is already policy language: white, taut enough to not bag, **not** 40 mm death mesh. We make **tension** mechanical: fail-**slack** when a flying-fox-sized body is in the net. Opposite of a security system.

### Non-human stakeholders
- **Primary — grey-headed flying-fox (and other fruit-bats).** Need the net to go slack and drop them out, not tighten.
- **Secondary — birds in the same net.** Same slack.
- **Human fruit.** Not the beneficiary of tension.

### Project idea
1 m demo frame, white knitted net, spring-loaded roller + MG90S pawl.

1. `TAUT_OK` — load in fruit-weight band; pawl holds.
2. `BODY_IN_NET` — load spike + optional IR break-beam → pawl **releases**, net slacks.
3. `NIGHT_SENSITIVE` — lux night: lower the load threshold (bats).
4. `WIND` — anemometer/BoM: ignore short gusts (hysteresis).
5. `RESET` — human cranks; limit switch says taut again.
6. `FAULT` — fail-**slack** (pawl open). Maintainer amber.

### One-line architecture
Tension + night + (optional) body size → ESP32-S3 → release a pawl. Fail-slack. Not an alarm.

```
 WORLD     net load / body           night lux              wind
               │                        │                    │
 SENSE      load cell or analog         BH1750              BoM / switch
            strain (HX711)              IR break-beam
 FUSE                    ESP32-S3
 ACTUATE    MG90S pawl  (spring does the slack)
 DEMO       sandbag “bat” dropped into net
```

### Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| HX711 + 1 kg load cell **or** analog FSR | HX711 GPIO 4/10 or ADC1 | load_g | fruit vs body |
| BH1750 | I2C | lux | night = bat hours |
| IR break-beam across the bag | GPIO 12 | beam_broke | confirm a body, not wind |
| BoM wind | NVS | gust | ignore <2 s spikes |
| FSM | MG90S | pawl engage/release | spring slacks the net |
| Human crank | limit | taut_ok | reset |

### Hardware stack

| Layer | Parts | Notes |
|---|---|---|
| Enclosure | Timber frame 1×1 m, white knitted **wildlife-friendly** mesh (cite gauge), roller + torsion spring | The net **is** the interface |
| Sense | HX711 + load cell (preferred) or FSR on ADC1; BH1750; IR gate | HX711 is not ADC2; clock/data on GPIO 4/10 |
| Actuate | MG90S as **pawl only** — spring stores the energy | Do not ask a 2.5 kg servo to catch a bat |
| Demo | 300–500 g sandbag with a bat silhouette | Never a live animal |

**Pin overrides.** HX711 DT/SCK on GPIO 4/10. Servo GPIO 7. IR GPIO 12. No pump.

**BOM delta.** HX711, load cell, white net (correct mesh), torsion spring, sandbag. **Do not** buy 40 mm hexagonal “bird net” — that is the problem.

### Software stack

`task_load` 20 ms · `task_lux` · `task_beam` · `task_api` (wind) · `task_fsm` · `task_actuate`.

Load: median 200 ms. Night threshold lower than day (fruit theft vs bat). Wind: require load **and** beam, or load held >1.5 s.

```
   TAUT_OK
      │ night AND (load > L_BAT or beam)
      │ day  AND (load > L_BODY and beam)   // ignore fruit bump
      ▼
   BODY_IN_NET (pawl release, net slacks) ── human reset ──► TAUT_OK
      │
   WIND_HOLD (gust, no beam) ── back to TAUT_OK
   fault or brown-out ──► FAIL_SLACK
```

Fail-**slack**. Opposite of every burglar alarm you have written.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Pawl releases; spring slacks net without MCU power (mechanical fail-slack) | 3 |
| 8 | BoM wind ignores a yank <1 s | 2 |
| 9 | Sandbag at “night” lux → slack; apple-weight bump does not | 1+2 |
| 13 | Cite WIRES/DCCEEW mesh; show 40 mm as the anti-pattern | 4 |

### Vision Pro
Entanglement volume in the net; gaze “this mesh size traps a thumb.” Headset does not fire the pawl.

### Risks
Live bats: never. Spring energy: keep fingers clear; document in the manual. Do not claim you certified backyard nets.

---

## 12. Stingless-bee hive door

### Challenge statement
Native *Tetragonula* hives overheat and take rain through the entrance; foragers still need a door that is open at the right temperature. Insect ACI: the hive body is the enclosure. No “smart app for beekeepers.”

**Literature honesty.** Designed from the lab outward unless you cite a local keeper / *Tetragonula carbonaria* thermal papers. Demo on a **empty** hive analog or a keeper-supplied observation hive with consent — not a feral nest.

### Non-human stakeholders
- **Primary — foragers.** Entrance open in the foraging band, closed in rain/heat spike.
- **Secondary — brood.** Thermal inertia; door hysteresis so you don’t chatter.
- **Human keeper.** Amber only; no phone.

### Project idea
Printed hive-box analog (standard Australian *Tetragonula* box proportions).

1. `FORAGE_OPEN` — T in band, dry, day → door open.
2. `HEAT_SHUT` — T_hive high → door toward closed (vent strategy: **small** upper vent stays, entrance reduces — cite, don’t invent).
3. `RAIN_SHUT` — rain sensor → entrance closed.
4. `COLD_SHUT` — night/cold.
5. `HYSTERESIS` — 2 °C / 5 min before any move.
6. `FAULT` — fail-**park** last pose; amber. Do **not** seal a live hive you do not own.

### One-line architecture
Hive temp + rain + clock → ESP32-S3 → entrance slider with hysteresis. The box is the UI.

```
 WORLD     hive air / brood analog     rain           day
               │                        │              │
 SENSE      DS18B20 ×2                  rain drop      BH1750
 FUSE                    ESP32-S3
 ACTUATE    MG90S entrance slider       optional upper vent
 DEMO       heat gun + spray bottle; no live colony required
```

### Hardware stack
DS18B20 ×2 (lid / entrance), DHT22 optional, rain analog (leaf-wetness or cheap drop sensor on ADC1), BH1750, ESP32-S3, MG90S slider, PETG box in hive proportions, beeswax-coloured material language.

**Do not** put electronics in honey. Probe wells only.

**BOM delta.** Dummy hive box, rain sensor, second DS18B20. No camera (foragers are not a classifier demo).

### Software stack

`task_probe` 2 s · `task_rain` 200 ms · `task_lux` · `task_fsm` · `task_actuate`.

**Hysteresis is the product.** Minimum 5 minutes between door moves. States: `OPEN` `HEAT_REDUCE` `RAIN_SHUT` `NIGHT_SHUT` `FAULT_PARK`.

API optional: BoM max T → pre-reduce entrance before the afternoon spike (feedforward, same grammar as idea 1). Fail-**park**. If you ever used a live observation hive, fail-**open** at forage hours if probes die — write that explicitly; dummy boxes can fail-shut.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Slider open/closed; 5 min lockout in firmware | 2+3 |
| 8 | BoM max-T pre-reduces | 2 |
| 9 | Heat gun → reduce; spray → shut; wait 5 min (mid-check) | 1 |
| 13 | Empty box; “no beekeeper app” | 4 |

### Vision Pro
Heat volume inside the box; gaze on the entrance as insect ACI. Gaze does not move the door.

### Risks
Live stingless bees: ethics + keeper. Do not seal a real colony with a stuck servo. Dummy is the honest Week 13 path.

---

## 13. Native-seedling drought drip

### Challenge statement
Heat and dry soil kill newly planted Sydney woodland tubestock while timer irrigation either drowns roots or misses the heatwave. The plant is the user. Soil moisture is the sensed body; BoM heat is feedforward. A drip that can stay **off**. Enclosure looks like a watering stone, not a gadget.

### Non-human stakeholders
- **Primary — the seedling (Sydney woodland analog: *Themeda*, *Lomandra*, or a named local tubestock).** Need water in the root zone when dry **and** heat is coming; not a swamp.
- **Secondary — remnant soil biota.** No over-water.

### Project idea
Stone-look drip puck at a pot.

1. `OFF` — default. Moisture OK or night-cool.
2. `FEEDFORWARD` — BoM heatwave + soil drying → drip **before** wilt.
3. `DRY_LIVE` — soil below threshold in heat of day → short PWM drip.
4. `WET_REFUSE` — soil wet → refuse (same justice as idea 9).
5. `EMPTY` — tank empty → amber, off.
6. `FAULT_OFF`.

### One-line architecture
Soil moisture + forecast heat → ESP32-S3 → drip MOSFET. Fail-**off**. Plant is the user.

```
 WORLD     root-zone moisture         heat forecast / air
               │                            │
 SENSE      analog soil probe            DHT22 + WiFi BoM
 FUSE                    ESP32-S3
 ACTUATE    5 V drip + MOSFET            stone enclosure
 DEMO       dry pot vs wet pot; hairdryer as “heatwave”
```

### Hardware stack
Capacitive soil moisture on **ADC1 GPIO 1** (not the rusting resistive forks if you can help it), DHT22 GPIO 4, ESP32-S3, 5 V pump or gravity solenoid, 0.5 L tank, PETG watering-stone, tube to pot.

**BOM delta.** Capacitive soil sensor, pot + woodland analog plant (nursery tubestock you **own**), tank. Optional second probe for hysteresis.

### Software stack

`task_soil` 2 s (median, temperature-compensate if you can) · `task_air` · `task_forecast` 10 min · `task_fsm` · `task_actuate`.

Calibrate **this** soil mix — raw ADC is not “% water.” Two pots in the video: wet refuse vs dry drip.

States: `OFF` `FEEDFORWARD` `DRY_LIVE` `WET_REFUSE` `EMPTY` `FAULT_OFF`.

Burst 10 s drip / 15 min off in demo time-lapse. API down: only `DRY_LIVE`, never invent a heatwave.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Pump 10 s then off from serial | 3 |
| 8 | BoM heat + drying soil → drip; wet soil refuses | 2 |
| 9 | Two pots side by side (mid-check) | 1+4 |
| 13 | Stone reads as landscape, not a gadget; plant is the user in ESD | 4 |

### Vision Pro
Root-zone moisture volume in the pot. Gaze does not start the pump.

### Risks
Over-claiming restoration. One pot is not Cumberland Plain recovery. Empty tank. Do not water someone else’s remnant.

---

## 14. Phytophthora boot-gate

### Challenge statement
Walkers carry *Phytophthora cinnamomi* and other soil pathogens into remnant woodland and kill proteaceous plants that cannot move. Commonwealth *Arrive Clean, Leave Clean* is the actual intervention. Human is the vector; the gate is a **ritual**. Strongest plant/Country essay in the 20. Pick **one** pathogen in the report (Phytophthora **or** chytrid, not both gadgets).

### Non-human stakeholders
- **Primary — proteaceous plants / sandstone woodland (Wollemi is 110 — name it as listed context, demo on a common analog).** Need dirty boots not to enter.
- **Country.** Tangible consent; do not costume.

### Project idea
PETG boot-bath + turnstile on a demo boardwalk plank.

1. `WAIT` — path latched.
2. `WASH` — boots in bath; turbidity/conductivity says soil came off.
3. `CLEAN_OPEN` — latch opens for N seconds.
4. `STORM_OVERRIDE` — BoM heavy rain: documented human bypass (don’t freeze people) — write it.
5. `SKIP` — if someone steps around, optional beam: still a **ritual**, not a prison.
6. `FAULT` — fail-**closed** for the path analog **or** fail-open with amber if this is a fire-trail ethics issue — **pick one and justify**. Recommended for a **demo plank**: fail-closed. For a story about real parks: fail-open + “ranger.”

### One-line architecture
Wash-water dirt + (optional) lux-day → ESP32-S3 → latch. Path does not open until water says the boot is clean. Gaze never unlocks.

```
 WORLD     boot soil in water           walker present         rain
               │                             │                  │
 SENSE      analog turbidity / TDS        LD2410 or mat        BoM
 FUSE                    ESP32-S3
 ACTUATE    MG90S turnstile / latch
 DEMO       dirty vs rinsed boot (your own shoe)
```

### Hardware stack
ESP32-S3, analog turbidity **or** TDS on ADC1, BH1750 (day use), LD2410 or pressure mat, MG90S latch, PETG boot-bath, grate, dirty-soil tray (potting mix you control). BoM rain override.

**BOM delta.** TDS or turbidity pair, boot-bath tub, latch hardware, a pair of **sacrificial** boots.

### Software stack

States: `WAIT` `WASHING` `CLEAN_OPEN` `STORM_BYPASS` `FAULT`.

Wash complete: turbidity **peak then drop** (soil came off, then water cleared) **or** timed scrub + TDS change. Cheap version: limit switch “boots in” + 10 s + turbidity below T_clean.

Fail-**closed** on the demo. API down: still wash-to-open; skip storm bypass.

**AVP may show “what you would carry” — must not unlock.**

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Latch opens only after GPIO “wash done” stub | 3 |
| 8 | BoM storm → documented bypass LED + latch open | 2 |
| 9 | Dirty boot fails; rinsed boot opens (mid-check) | 1+4 |
| 13 | Country/plant essay; *Arrive Clean, Leave Clean*; no prison aesthetic | 4 |

### Vision Pro
Ghost soil on the boot tread. Gaze does not unlock the latch.

### Risks
Do not trap people on a real trail. Demo plank only. Don’t costume Country. One KTP, one pathogen.

---

## 15. Blue-tongue bait cover

### Challenge statement
Metaldehyde snail bait is an ecological trap: eastern blue-tongue skinks eat poisoned snails (and sometimes the pellets) and die. Close the trap when the non-human **beneficiary** arrives. Lizard-sized signature vs snail-only aperture. Suburban, demoable, morally sharp.

**Do not use real metaldehyde in the demo.** Inert pellets / printed discs. The ethics is the point.

### Non-human stakeholders
- **Primary — eastern blue-tongue (*Tiliqua scincoides*).** Need bait inaccessible.
- **Snail.** Pest in the garden story; not a stakeholder we protect with a lid — the **aperture** is snail-only when the lid is “garden mode.”
- **Human gardener.** Must still be able to bait snails without building a skink feeder.

### Project idea
Stone-look bait station.

1. `SNAIL_APERTURE` — default: holes snail-sized, lid down.
2. `SKINK_PRESENT` — mmWave / ToF skink-sized + optional image → lid **locks down harder** / inner tray withdraws.
3. `NIGHT_MOLLUSC` — snails mainly night: aperture open at night if no skink.
4. `DAY_SKINK` — blue-tongues diurnal: extra-safe day lock.
5. `HUMAN_SERVICE` — key / magnet for the gardener (explicit, rare).
6. `FAULT` — fail-**closed** (cover on).

### One-line architecture
Body size × clock → ESP32-S3 → cover / tray. Non-human arrives → trap closes. Fail-closed.

```
 WORLD     lizard-sized body         clock / lux          snail hours
               │                        │                    │
 SENSE      LD2410 + ToF              BH1750               (same)
            optional cam
 FUSE                    ESP32-S3
 ACTUATE    MG90S cover / inner tray
 DEMO       stuffed/printed skink vs snail analog; inert pellets
```

### Hardware stack
BH1750, LD2410, optional VL53L0X for low profile, optional camera, MG90S, PETG station with **measured** snail holes vs skink-head width, inert pellets.

**BOM delta.** Printed *Tiliqua* analog, snail analog, bait-station housing. **No poison.**

### Software stack

States: `SNAIL_NIGHT` `DAY_LOCK` `SKINK_SHUT` `SERVICE` `FAULT_CLOSED`.

Day → lock (skinks). Night + no large body → snail aperture. Large body any time → shut. Image API: skink vs cat vs nothing — cat is not a reason to open.

Fail-**closed**. API down: stay day-lock / closed rather than open a buffet.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Cover shut/open; holes sized in CAD | 3 |
| 8 | Optional image: skink analog vs empty | 2 |
| 9 | Day: shut; night + no body: aperture; stuffed skink: shut | 1+2 |
| 13 | “No metaldehyde on campus”; two ESD tables (snail control vs skink) | 4 |

### Vision Pro
Skink body vs pellet. Gaze does not open the tray.

### Risks
Poison. Live lizards. Don’t teach a better way to bait wildlife. Inert demo only.

---

## 16. Little-penguin dusk shore-gate

### Challenge statement
Little penguins coming ashore at dusk (Manly / North Harbour) are flushed or killed by dogs and foxes on the same sand. Waddle vs gait. Gate reads body + dusk and **fails closed to dogs, open to penguin-sized low profiles**. Live animals never used; stuffed analog + gait playback. Famous national issue, campus-safe demo.

### Non-human stakeholders
- **Primary — little penguin (*Eudyptula novaehollandiae*).** Need a low, open waddle slot at dusk.
- **Dogs / foxes.** Closed. Not ultrasound.

### Project idea
Sand-coloured low fence analog ~0.8 m wide.

1. `DAY_SHUT` — people/dogs on the beach analog.
2. `DUSK_PENGUIN` — lux dusk + low profile + slow gait → slot open.
3. `DOG_SHUT` — tall / fast gait → shut.
4. `HUMAN` — never pinch.
5. `FAIL` — **closed to dog-class, open to penguin-class geometry**. If classifier dies: rely on **geometry** (penguin fits, dog doesn’t) and dusk clock. Write this: geometry is justice; ML is optional.

### One-line architecture
Dusk × height × gait → ESP32-S3 → low gate. Geometry first. Fail closed to dogs, open to penguin slot.

```
 WORLD     dusk                 height / gait              class
               │                      │                      │
 SENSE      BH1750               LD2410 + ToF             optional cam / IMU playback
 FUSE                    ESP32-S3
 ACTUATE    MG90S low flap  +  fixed penguin slot
 DEMO       stuffed penguin analog + dog analog; gait from video on a phone into the mic/IMU
```

### Hardware stack
Same family as 3 and 10, **third enclosure**. Fixed slot sized to penguin; servo flap on the **dog** opening only. BH1750, LD2410, ToF height, optional camera, ESP32-S3, MG90S.

**Gait demo:** do not need ML on-device for W9. Play a “waddle” vs “trot” by moving the analog at two speeds in front of mmWave (speed from range derivative). W8 image API is Credit.

**BOM delta.** Penguin plush/print, dog plush, sand-coloured PETG, two openings (low slot always open at dusk; tall gate actuated).

### Software stack

FSM: `DAY_SHUT` `DUSK_SLOT` `DOG_SHUT` `HUMAN_SAFE` `API_DOWN_GEOM` `FAULT`.

`API_DOWN_GEOM`: dusk → penguin slot remains physically open; tall flap stays **closed**. That is the Week 13 sentence.

Never live penguins. Manly colony is not your lab.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Tall flap shut; low slot always clear | 3 |
| 8 | Image dog vs penguin analog | 2 |
| 9 | Dusk-lux + low slow body → slot story; tall fast → flap shut | 1+2 |
| 13 | National issue + campus analog + no live animals | 4 |

### Vision Pro
Eye-height of a penguin vs a dog on the same sand. Gaze does not shut the gate.

### Risks
Tourist colony politics. Do not claim you installed at Manly. Foxes jump — geometry isn’t perfect; say so.

---

## 17. Sugar-glider night crossing

### Challenge statement
Fragmented suburban canopy leaves *Petaurus* facing gaps they will only cross in low wind at night. Wind is the mode. A pole/line that **extends only when night + wind-safe** — otherwise it is a false invitation. Tangible infrastructure, not a tracker on a glider.

### Non-human stakeholders
- **Primary — sugar glider / squirrel glider analog.** Need a crossing that exists only when usable.
- **Owl / cat.** A permanently deployed rope at 1.5 m can be a predator perch — **retract by day**.

### Project idea
Two poles, 1.2 m gap, retractable line.

1. `RETRACTED` — default day.
2. `NIGHT_CALM` — lux night + wind < threshold → line extends.
3. `WIND_RETRACT` — gust → pull back (don’t fake a crossing).
4. `OCCUPIED` — ToF/mmWave on the line → **do not retract** (animal on the rope).
5. `FAULT` — fail-**park**; if occupied, stay extended; if empty, retract. Write both.

### One-line architecture
Night × wind × occupancy → ESP32-S3 → extend/retract. Wind as mode. No tracker on a glider.

```
 WORLD     night                 wind                    body on line
               │                   │                          │
 SENSE      BH1750              anemometer or BoM          ToF / mmWave
 FUSE                    ESP32-S3
 ACTUATE    MG90S reel / extending arm
 DEMO       fan = wind; night-lux; stuffed glider on the line
```

### Hardware stack
BH1750, anemometer (analog cup **or** BoM wind as W8-only — a cheap cup is more honest for “wind is the interface”), LD2410 or ToF along the line, ESP32-S3, MG90S reel **plus** a spring or second servo, two poles.

**Safety.** Line must not be a neck-height clothesline in a corridor. Demo in a marked 1.2 m bay.

**BOM delta.** 2 poles, line, optional analog anemometer, stuffed glider.

### Software stack

States: `DAY_RETRACT` `NIGHT_EXTEND` `WIND_RETRACT` `OCCUPIED_HOLD` `FAULT`.

Wind hysteresis 30 s. Occupied overrides wind (don’t fling a body). API-only wind: cache; if API down at night, **do not extend** (fail-safe against false invitation) **unless** you have a local anemometer.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Reel out/in; occupied ToF freezes retract | 3 |
| 8 | BoM wind or fan+switch | 2 |
| 9 | Dark + still → extend; fan → retract; toy on line → hold | 1+2 |
| 13 | “Not a GPS collar”; false invitation is the IxD point | 4 |

### Vision Pro
Canopy gap as a volume; wind field. Gaze does not extend the line.

### Risks
Clothesline hazard. Live gliders: no. Retract under load: mechanical design, not firmware bravado.

---

## 18. Water-dragon basking rock

### Challenge statement
Eastern water dragons on campus sandstone lose basking when people startle them off the rock, then cannot rewarm. Do not startle. Proxemic slow screen for humans; the rock never jerks while a dragon-sized body is on it. Thermal comfort is the non-human output; people get a quiet barrier, not an alarm.

### Non-human stakeholders
- **Primary — eastern water dragon (*Intellagama lesueurii*).** Stay on the rock; rewarm.
- **Human walker.** Slow, readable barrier — not a scarecrow.

### Project idea
Sandstone-look rock analog, small “screen” / frond that rises **slowly** when a person is 3–5 m away, **frozen** if a dragon-sized heat/body is on the rock.

1. `BASK` — IR or contact says dragon-sized on rock; human far → do nothing.
2. `SLOW_SCREEN` — person in 3–5 m, rock **empty** → rise 10–20 s.
3. `HOLD_IF_OCCUPIED` — person approaches but body on rock → **motor inhibit**.
4. `HEAT_ASSIST` (optional, analog only) — dark rock already does this; a tiny heater is ethically noisy — **prefer passive PETG thermal mass**.
5. `NIGHT` — park.
6. `FAULT` — park; never jerk.

### One-line architecture
Person range × body-on-rock × lux → ESP32-S3 → slow barrier. Motor must not startle. Fail-**park**.

```
 WORLD     walker distance         dragon-sized body / heat      day
               │                         │                        │
 SENSE      LD2410 range              MLX90614 or ToF            BH1750
 FUSE                    ESP32-S3
 ACTUATE    MG90S slow frond / screen     (no siren, no LED at the animal)
 DEMO       stuffed dragon on rock vs person approach
```

### Hardware stack
LD2410 (range), MLX90614 or ToF on the rock surface, BH1750, ESP32-S3, MG90S with **software speed limit** (2–3°/s), sandstone PETG, stuffed/printed dragon.

**No alarm speaker. No sudden 180° slam.**

**BOM delta.** MLX90614, sandstone print, dragon analog.

### Software stack

`task_range` 100 ms · `task_rock` 200 ms · `task_lux` · `task_fsm` · `task_actuate` (slew-limited).

States: `EMPTY_IDLE` `BASK_HOLD` `SLOW_SCREEN` `NIGHT_PARK` `FAULT_PARK`.

If `rock_occupied`, `cmd.servo` frozen even if `SLOW_SCREEN` requested. Occupied detected by IR delta **or** ToF. Hysteresis so a tail flick doesn’t chatter.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Servo slew 2°/s; hard-code inhibit | 2+3 |
| 8 | Optional: none required; BoM unused is OK — Credit API can be skipped if you argue implicit-only, **or** use a simple presence API | 2 |
| 9 | Toy on rock: person approach does **not** move; empty rock: slow rise | 1 |
| 13 | Quiet barrier, not an alarm; campus dragons not handled | 4 |

Week 8: if you need a Credit API, use a **human-pose** image API on the path (person vs empty) while IR owns the rock — still no ChatGPT.

### Vision Pro
Dragon thermal ghost on the real rock. Gaze does not move the frond.

### Risks
Startling real dragons with a demo on their rock — use a **table analog**, not the Quad sandstone with a live animal. Heater ethics.

---

## 19. Oystercatcher high-tide roost rope

### Challenge statement
Pied oystercatchers (and other shorebirds) are flushed off shrinking high-tide roosts by off-leash dogs on Sydney/Botany shores. Tide clock + dog presence. A rope that deploys as ambient architecture at high tide. Not a siren. Tide is the most elegant W2 implicit input in the set.

### Non-human stakeholders
- **Primary — pied oystercatcher / roosting shorebirds.** Undisturbed high-tide roost.
- **Dogs.** Rope is a social/physical cue to humans, not a weapon.

### Project idea
1.5 m demo “shore,” rope on two posts.

1. `LOW_TIDE` — rope down / slack (beach open analog).
2. `HIGH_TIDE` — tide API or ultrasonic “water” in a tray → rope **up**.
3. `DOG` — mmWave dog-class on the roost side → rope stays up; optional extra drop-arm. **No siren.**
4. `BIRD_ON_ROOST` — optional: don’t whip the rope if a body is on the post.
5. `FAULT` — fail-**up** at predicted high tide (protective), down only if tide API says low **and** no fault.

### One-line architecture
Tide clock + dog presence → ESP32-S3 → rope as architecture. Implicit tide. Not an alarm.

```
 WORLD     tide                     dog on roost              bird on post
               │                         │                         │
 SENSE      tide API + tray ToF        LD2410                    ToF
 FUSE                    ESP32-S3
 ACTUATE    MG90S rope winch
 DEMO       jug fills “tide” tray; stuffed dog vs stuffed bird
```

### Hardware stack
ESP32-S3, WiFi tide/BoM (Sydney tide JSON or Open-Meteo marine), tray + ToF as **visible** analog of tide (assessors must see water, not only an API), LD2410, MG90S winch, two posts, rope, PETG “sand.”

**BOM delta.** Tide analog tray, rope, bird analog, dog analog.

### Software stack

`task_tide_api` 5–15 min · `task_level` 200 ms · `task_radar` · `task_fsm` · `task_actuate`.

States: `LOW` `RISING` `HIGH` `HIGH_DOG` `OCCUPIED_HOLD` `FAULT_HIGH`.

Local tray lets you demo without waiting for Botany high tide. API is Credit + the essay. Occupied post: freeze winch.

Fail-**up** if API confused during a demo “high” tray.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Winch up/down slowly | 3 |
| 8 | Tide JSON + tray override | 2 |
| 9 | Fill tray → rope up; stuffed dog doesn’t trigger a speaker | 1+4 |
| 13 | Tide as implicit input; no siren | 4 |

### Vision Pro
Tide volume over the sand; dog flush cone. Gaze does not winch.

### Risks
Real roosts and real dogs: do not deploy. Rope as trip hazard. Shorebird ethics: stuffed only.

---

## 20. Echidna mow-pause stake

### Challenge statement
Short-beaked echidnas forage diurnally in lawns and remnant edges and are injured by mowers; soil is compacted. A flag for the **human operator**, not a wearable on the animal. Vibration + clock + soil. The stake is W9 tangible + W10 “wearable” logic inverted (the person pushing the mower is the one who must notice).

### Non-human stakeholders
- **Primary — short-beaked echidna (*Tachyglossus aculeatus*).** Need the mower to pause in this patch.
- **Soil / invertebrates.** Compaction story in the essay.
- **Operator.** Must see a flag / feel a haptic — not a phone app while mowing.

### Project idea
Survey-stake in a turf tray.

1. `QUIET` — flag down.
2. `FORAGE_WINDOW` — day clock + soil vibration (echidna-like) → flag **up**, optional haptic band on the **human**.
3. `MOWER_NEAR` — mmWave large metal/fast → flag urgent (faster deploy), still no siren at wildlife.
4. `NIGHT` — down (echidnas may still move; keep the story diurnal as cited).
5. `FAULT` — flag up (fail visible to the operator). Fail-**up**.

### One-line architecture
Vibration + clock + soil + mower presence → ESP32-S3 → flag (and optional human haptic). Wearable logic inverted.

```
 WORLD     ground vibration           day clock / soil         mower / person
               │                           │                        │
 SENSE      piezo / IMU analog          BH1750 + DS18B20         LD2410
 FUSE                    ESP32-S3
 ACTUATE    MG90S flag                 optional BLE haptic band (human)
 DEMO       tap the turf analog; toy mower approach
```

### Hardware stack
Piezo disc or IMU (I2C MPU-6050) in a spike, BH1750, optional soil moisture (compaction/wetness essay), LD2410 toward the “mower” aisle, ESP32-S3, MG90S flag, PETG survey-stake, turf tray. Optional BLE haptic to a phone **held by the operator** — if BLE isn’t up, the flag alone is Pass.

**BOM delta.** Piezo or IMU, flag printed bright (operator must see it), turf tray, toy mower.

**Do not** put a tracker on an echidna.

### Software stack

`task_vib` 10–20 ms (band-pass: reject continuous mower rumble vs snuffle pulses — demo can be “three taps”) · `task_lux` · `task_radar` · `task_fsm` · `task_actuate`.

States: `DOWN` `FLAG_FORAGE` `FLAG_MOWER` `NIGHT_DOWN` `FAULT_UP`.

Credit API: BoM is weak here; use a **classifier** on vibration as stretch, or skip API and argue the unit’s W8 with a simple HTTP “mower roster / campus grounds calendar” — better: **image API** on the aisle (mower vs person vs empty).

Fail-**up** (visible). False positive = extra pause (safe). False negative = the ethical residual; say so.

### Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Flag snaps up from serial; visible at 5 m | 3 |
| 8 | Image or HTTP calendar stub | 2 |
| 9 | Three taps on turf → flag; toy mower → urgent flag; no animal wearable | 1+4 |
| 13 | Inverted wearable essay; never a live echidna | 4 |

### Vision Pro
Forage snuffle underground ghost. Gaze does not raise the flag. Optional: show the operator’s haptic as a human-only layer.

### Risks
Live echidnas and real mowers: no. Vibration ML will be messy — keep W9 as taps + flag. Don’t claim you instrumented grounds staff.

---

## Cross-walk: fail mode + Credit API + enclosure (11–20)

| # | Fail | W8 API | Enclosure is… |
|---|---|---|---|
| 11 | slack | BoM wind | the net |
| 12 | park (dummy) / open if live hive | BoM max T | the hive |
| 13 | off | BoM heat | a watering stone |
| 14 | closed (demo) | BoM rain bypass | a boot ritual |
| 15 | closed | image skink | a bait stone |
| 16 | geom: slot open, tall shut | image gait/class | a shore slot |
| 17 | don’t extend if no wind sense | BoM wind | a retractable crossing |
| 18 | park | optional pose API | a sandstone rock |
| 19 | up at high tide | tide JSON | a roost rope |
| 20 | flag up | image mower / calendar | a survey stake |

**1–10 packs:** [[packs_01-10_en]]
