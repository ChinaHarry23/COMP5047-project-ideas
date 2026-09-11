---
course: COMP4447 / COMP5047
title: "20 ideas — ecological challenge + interaction design (EN)"
updated: 2026-09-12
lang: en
aliases:
  - 20 ideas index EN
---

# 20 ideas — one-sentence challenge + what stands out in interaction design

> **中文**：[[00_index_cn]]
> **Full proposal + hardware/software architecture + implementation plan for each idea**：[[packs_en]]
> Shared MCU / pins / firmware layers / fail-mode grammar：[[architecture_shared_en]]
> Detailed packs 1–10：[[packs_01-10_en]] · 11–20：[[packs_11-20_en]]
> Brief: *Designing for a More-Than-Human Australia*. Vision Pro = second station, not the Pass product. Old path-light bollard: on hold.

**Team (copy into whichever proposal you submit).** Five USYD MCS students. Tick **exactly one** PM.

| Name | Email | SID | PM |
|---|---|---|---|
| Xinlong Bao | xbao0618@uni.sydney.edu.au | 550048909 | ☐ |
| Tianqi Wang | twan0795@uni.sydney.edu.au | 550216999 | ☐ |
| | | | ☐ |
| | | | ☐ |
| | | | ☐ |

---

## How to read this page

**Ecological challenge** = the Australian situation in one sentence (Distinction wants this grounded in literature later).

**What stands out in IxD** = the unit’s interaction ideas (W2 ESD / implicit / proxemics / state machines; W5–9 tangible enclosure; W6–7 gesture vs motor; W8 API; W10–12 wearable / gaze / MR). Not a list of sensors.

Pass artefact is always: MCU + ≥2 sensors + non-screen actuation + fabricated enclosure. Headset is inquiry.

---

## 1–8 (from the first shortlist)

### 1. Camp-edge heat refuge
**Challenge.** Grey-headed flying-foxes in Sydney camps die in heatwaves when air and roost microclimate cross published heat-stress thresholds, especially pups.
**IxD that stands out.** *Feedforward, not feedback:* shade/mist fire from a forecast (W8) before panting starts. Default **off**. The hanging leaf is the interface (W9). Gaze/MR show heat you cannot see; they must not fire the pump.

### 2. Brush-turkey mound kiln
**Challenge.** Suburban brush-turkeys already farm mound temperature (~33 °C) for eggs; people rake the mounds and collapse the clutch.
**IxD that stands out.** *Join an existing animal interface* (ACI): temperature **is** the dialogue, not a button. A physical “do not rake” flag is a tangible message to humans. Strongest implicit-interaction essay.

### 3. Dusk cat-gate for possums
**Challenge.** Outdoor cats are a leading Australian extinction driver, while brushtail possums still need night fence runways through the same yards.
**IxD that stands out.** *Proxemics as geometry:* identity × dusk × body size decide a gap. **Fail-open for the possum** if the image API dies — justice encoded in the state machine, not a slogan.

### 4. Glass-strike shutter
**Challenge.** Dawn glass on campus and city buildings presents a fly-through illusion that kills small woodland birds.
**IxD that stands out.** *The window is the UI* (calm ubicomp). Default shutter **down** at civil twilight. Best mixed-reality overlay in the set (bird vs human view of glass) without making MR the product.

### 5. First-flush creek delay
**Challenge.** The first minutes of Sydney stormwater carry oil, silt, and faeces into creeks that still hold eels and riparian life.
**IxD that stands out.** *Water as the display:* two trays make first-flush vs clean-flow legible without a screen. Country/water framing without costume. Strong W2 state machine (dry / dirty / clean / settle).

### 6. Noisy-miner clutter baffles
**Challenge.** Noisy miners dominate open east-coast plantings and exclude small insectivores that need visual clutter, not more noise.
**IxD that stands out.** *Refuse the speaker.* Clutter vanes are the medium. Implicit audio context (W2, W8 BirdNET). Gesture lecture applied in reverse: wrens do not owe you a gesture set.

### 7. Honeyeater bath that is not a cat bowl
**Challenge.** Heatwave waterers help honeyeaters and simultaneously build a sit-and-wait hunting table for outdoor cats.
**IxD that stands out.** *Two Desired states in conflict* — write ESD twice. The lid is a tangible vote for the bird. Heat (analogue) vs predator (presence/image) in one object.

### 8. Harbour silt valve (oyster tray)
**Challenge.** Rain pulses dump silt and freshwater into Sydney Harbour and stress the feeding window of Sydney rock oysters.
**IxD that stands out.** *One calm valve in a system that is already pervasive* (the harbour). Tank demo is readable at three metres. MR shows filtering; motors do the analog.

---

## 9–20 (expanded set)

### 9. Bell-frog hydroperiod pump
**Challenge.** Green and golden bell frogs need an ephemeral pond that stays wet long enough to metamorphose, but a always-full pond becomes fish, mosquito, and flush habitat that kills tadpoles.
**IxD that stands out.** *Hydroperiod as the product*, not a fountain. Rain + water level decide a pump that can **refuse to fill**. Demo species may be a common marsh frog; named stakeholder stays *Litoria aurea* with that honesty in the report.

### 10. Bandicoot dawn underpass
**Challenge.** Long-nosed bandicoots still forage at remnant edges, but fences, cats, and dawn traffic cut their ground runways.
**IxD that stands out.** *Ground-level proxemics* (not a fence-top possum trick). A crawl geometry that a bandicoot fits and a cat does not. Dawn clock is the mode switch.

### 11. Wildlife-friendly fruit-net slack
**Challenge.** Backyard fruit nets kill grey-headed flying-foxes when mesh and tension turn the tree into a trap.
**IxD that stands out.** *Tension is the interface.* Fail-**slack** when a flying-fox-sized body is in the net. Opposite of a security system. WIRES/DCCEEW “wildlife-friendly netting” made mechanical.

### 12. Stingless-bee hive door
**Challenge.** Native *Tetragonula* hives overheat and take rain through the entrance; foragers still need a door that is open at the right temperature.
**IxD that stands out.** *Insect ACI:* the hive body is the enclosure. A door with hysteresis (don’t chatter). Heat + rain as implicit input. No “smart app for beekeepers” as the product.

### 13. Native-seedling drought drip
**Challenge.** Heat and dry soil kill newly planted Sydney woodland tubestock while timer irrigation either drowns roots or misses the heatwave.
**IxD that stands out.** *The plant is the user.* Soil moisture is the sensed body; BoM heat is feedforward. A drip that can stay **off**. Enclosure looks like a watering stone, not a gadget.

### 14. Phytophthora boot-gate
**Challenge.** Walkers carry *Phytophthora* and other soil pathogens into remnant woodland and kill proteaceous plants that cannot move out of the way.
**IxD that stands out.** *The human is the vector; the gate is a ritual.* Tangible consent with Country: the path does not open until wash-water says the boot is clean. Strongest plant/Country essay. Gaze on AVP can show “what you would carry,” not unlock the latch.

### 15. Blue-tongue bait cover
**Challenge.** Metaldehyde snail bait is an ecological trap for eastern blue-tongue skinks that eat poisoned snails (and sometimes the pellets).
**IxD that stands out.** *Close the trap when the non-human arrives.* Lizard-sized signature vs snail-only aperture. Suburban, demoable, morally sharp.

### 16. Little-penguin dusk shore-gate
**Challenge.** Little penguins coming ashore at dusk (Manly / North Harbour) are flushed or killed by dogs and foxes on the same sand.
**IxD that stands out.** *Waddle vs gait.* A gate that reads body + dusk and **fails closed to dogs, open to penguin-sized low profiles**. Live animals never used; stuffed analog + gait playback. Famous national issue, campus-safe demo.

### 17. Sugar-glider night crossing
**Challenge.** Fragmented suburban canopy leaves *Petaurus* facing gaps they will only cross in low wind at night.
**IxD that stands out.** *Wind as a mode.* A pole/line that **extends only when night + wind-safe** — otherwise it is not a false invitation. Tangible infrastructure, not a tracker on a glider.

### 18. Water-dragon basking rock
**Challenge.** Eastern water dragons on campus sandstone lose basking when people startle them off the rock, then cannot rewarm.
**IxD that stands out.** *Do not startle.* Proxemic slow screen for humans; the rock never jerks while a dragon-sized body is on it. Thermal comfort is the non-human output; people get a calm barrier, not an alarm.

### 19. Oystercatcher high-tide roost rope
**Challenge.** Pied oystercatchers (and other shorebirds) are flushed off shrinking high-tide roosts by off-leash dogs on Sydney/Botany shores.
**IxD that stands out.** *Tide clock + dog presence.* A rope that deploys as ambient architecture at high tide. Not a siren. Tide is the most elegant W2 implicit input in the set.

### 20. Echidna mow-pause stake
**Challenge.** Diurnal echidnas forage in lawns and remnant edges and are injured by mowers, plus soil compaction, during that window.
**IxD that stands out.** *A flag for the human operator, not a wearable on the animal.* Vibration + clock + soil. The stake is W9 tangible + W10 “wearable” logic inverted (the mower operator is the one who must notice).

---

## 24-hour pick (unchanged logic)

| Want | Pick |
|---|---|
| Best MR overlay | **4**, then **1**, then **5 / 14** |
| Best artefact if visionOS slips | **2**, **7**, **9**, **12** |
| Best proxemics / justice FSM | **3**, **10**, **16** |
| Best plant / Country | **13**, **14** |
| Best “this is Australia” literature | **1**, **3**, **6**, **16** |

Five-person split for any of them: hardware · firmware · CAD · docs/video · visionOS **after** Week 9.

---

## Hardware / software architecture and implementation (all 20)

Shared stack (ESP32-S3, default pins, FreeRTOS layers, fail-mode grammar, cart): [[architecture_shared_en]].

Per-idea **architecture map, pin/BOM delta, tasks, full FSM, W7–W13 acceptance tests, Week 13 demo script, Vision Pro boundary**: index [[packs_en]] · 1–10 [[packs_01-10_en]] · 11–20 [[packs_11-20_en]].
