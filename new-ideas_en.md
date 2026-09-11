---
course: COMP4447 / COMP5047
title: "New project ideas (EN) — schedule + Vision Pro"
updated: 2026-09-11
lang: en
aliases:
  - New ideas EN
---

# New project ideas — unit schedule + Vision Pro

> **中文版**：[[new-ideas_cn]]
> **Brief**: Designing for a More-Than-Human Australia.
> **Today**: last day of Week 6 (11 Sep 2026). Proposal was due Week 4 — a pivot needs tutor approval this week.
> **Old bollard topic**: disregard. Do not submit the Wildlife-First path light as the product.

Five University of Sydney **Master of Computer Science** students. Full brief size; extra members do not shrink the artefact. Tick **exactly one** project manager in `TEAM.md`.

---

## 0. What this set is for

The official brief still requires:

- a named **Australian ecological situation** and **non-human actors** as participants
- MCU + **≥2 sensor types** + **≥1 non-screen** feedback (sound, light, haptics, actuation)
- **not** a website, phone app, or screen-primary UI
- fabricated enclosure whose form/material belongs in that ecology (Credit+)
- context inference + adaptive behaviour + an **AI API** (Credit)

The **unit schedule** is a pipeline. Bake Weeks 1–6 in now; Weeks 7–8 are the Credit floor; Weeks 10–12 are where **Apple Vision Pro** belongs.

---

## 1. Vision Pro: second station, not the Pass product

Pass forbids a website, mobile app, or screen-primary interface. A visionOS app **as the product** fails that test.

Use Vision Pro as:

| Week | Taught topic | Headset job |
| --- | --- | --- |
| 6 | Gestural interaction | Manipulative pinch / deictic point — **inquiry**, not animal commands |
| 10 | Wearable interfaces | The headset **is** the wearable. Nothing worn by wildlife. Optional haptic band for humans only. |
| 11 | Gaze interaction | Gaze-dwell **reveals** a layer. Gaze must **not** fire animal-facing motors. |
| 12 | Mixed Reality | Overlay **registered to the fabricated object**. HD rubric: creative staging. |
| 13 | Showcase | Physical demo first; headset as optional second station. |

**Rule:** inquiry on the headset, care in the enclosure. If the headset does not boot in Week 13, servos/pumps/flaps still run. With five people, one workstream owns visionOS **only after** one hardware feature works (Week 9 mid-check).

Credit AI API = BoM / image classifier / BirdNET — not ChatGPT as the product.

---

## 2. How every idea spends the remaining weeks

| Wk | Taught topic | How the project uses it | Now |
| --- | --- | --- | --- |
| 1 | Ubicomp, visionaries, what comes after | Calm physical artefact in the world. AVP is inquiry, not the ubicomp product. | Taught |
| 2 | Devices, ESD, state machines, implicit, proxemics | Write Expected–Sensed–Desired for the **non-human**. Context → states, not a GUI. | Taught |
| 3 | Digital I/O, ESP32, LEDs, buttons | End-stops, maintainer LED, MOSFET gates. Not a screen UI. | Taught |
| 4 | Analogue I/O, PWM, `millis()`, smart lamp | ≥2 sensor types. PWM for pump/fan/LED. Non-blocking loop. | Taught |
| 5 | Digital fabrication, Fusion 360 | CAD enclosure whose form/material **is** the ecology (Credit + Distinction). | Taught |
| 6 | Gestures; WiFi + Bluetooth lab | ESP32 WiFi/BLE for nodes and APIs. Gestures on AVP, not on animals. | Taught |
| 7 | Haptics; controlling motors | Servo/pump/flap = non-screen feedback. Haptic band for humans only. | Next 2 wks |
| 8 | Intelligent systems; accessing APIs | Credit: BoM, image, or BirdNET. | Next 2 wks |
| 9 | Tangible interaction; mid-project check-in | Enclosure is the interface. One key feature works **without** a headset. | After break |
| 10 | Wearable interfaces | Vision Pro, or a tiny haptic band. Nothing on wildlife. | After break |
| 11 | Gaze interaction | Gaze-dwell to reveal a layer. Not a motor trigger. | After break |
| 12 | Mixed Reality; extended presentation | Register overlay to the object. Presentation aid. | After break |
| 13 | Showcase | Live physical demo first. Video 3–5 min. | After break |

Five-person split: hardware · firmware · CAD · docs/video · visionOS last.

---

## 3. Eight ideas

### 1. Camp-edge heat refuge

**Situation.** Sydney grey-headed flying-fox camps. Heatwaves kill pups; camps sit in the city.

**Non-human.** Grey-headed flying-fox (*Pteropus poliocephalus*, EPBC vulnerable). Need: shade + evaporative cooling when published heat-stress thresholds are crossed.

- **Sense → act.** DHT22 temp/RH (analogue, W4) + lux or MLX90614 IR of a fake canopy → servo leaf-shade + mist (W7). PWM pump. Default off.
- **AI API (W8).** BoM / Open-Meteo so shade **pre-opens** before the peak, not after panting starts.
- **Enclosure (W5/W9).** Fusion hanging “leaf”. The object is a canopy, not a gadget box.
- **Demo.** Heat-box on campus. Never mist a live camp.

**Syllabus.** W2 ESD + state machine (idle / forecast-heat / live-heat). W3 maintainer LED only. W6 ESP32 WiFi fetches forecast; BLE optional debug. W10 haptic band buzzes the **human** when the analog is in “stress” — animals never wear it.

**Vision Pro.** W12 thermal volume around the physical leaf (false-colour heat, not a dashboard). W6 pinch to scrub the next 6 h of BoM. W11 gaze-dwell on the leaf reveals who this is for (pup vs adult) — inquiry only. **Headset must not fire the mist.**

**If the headset dies.** Leaf still folds, mist still runs. Pass/Credit live without visionOS.

**Risk.** NPWS / ethics. Cite heat-stress papers. No real camp install.

---

### 2. Brush-turkey mound kiln

**Situation.** *Alectura lathami* farms mound temperature (~33 °C) across suburban Sydney. Humans rake mounds away.

**Non-human.** Eggs / embryos first; the male as the other operator. Need: a stable incubation band.

- **Sense → act.** DS18B20 in mulch + DHT22 air → servo vents / fan (W7). Physical “do not rake” flag when in-band (W9).
- **AI API (W8).** BoM rain+temp so vents do not fight a cold front.
- **Enclosure.** Thatched PETG shell filled with real mulch. Form **is** the mound.
- **Demo.** Sandbox + aquarium heater: 28 / 33 / 38 °C.

**Syllabus.** W2 implicit: the mound’s temperature is the dialogue, not a button. States: too-cool / in-band / too-hot. Strongest ACI write-up in the set.

**Vision Pro.** 33 °C isotherm as a volume inside the physical mound. Two-hand scale of egg depth. Gaze at the flag reveals why raking now would kill the clutch.

**If the headset dies.** Vents and flag still work. Best Week-13-proof artefact.

**Risk.** Do not bury electronics in a real mound. Campus analog only.

---

### 3. Dusk cat-gate for possums

**Situation.** Outdoor cats kill urban wildlife; brushtail possums still need night fence runways.

**Non-human.** Common brushtail possum (*Trichosurus vulpecula*). Need: a corridor open to a possum and closed to a cat after dusk.

- **Sense → act.** BH1750 dusk (W4 smart-lamp lineage) + mmWave/ToF on the rail → servo flipper (W7). No ultrasonic “pest” devices.
- **AI API (W8).** Image API: cat vs possum vs human vs empty. **Fail OPEN for the possum** if the API dies.
- **Enclosure.** Clamp-on timber/PETG branch on a demo fence.
- **Demo.** Stuffed cat vs stuffed possum vs person.

**Syllabus.** W2 proxemics (Vogel & Balakrishnan): identity × distance × orientation on a fence. ESD: expected (possum commute), sensed (body class + dusk), desired (gap geometry). W6 BLE if you split “see” and “actuate”.

**Vision Pro.** Drop the visitor’s eye-height to possum vs cat. Point at the gap to see who fits. **Gaze does not close the gate** — that would make a possum wait on a headset.

**If the headset dies.** Gate still classifies and moves. Fail-open is the Credit story.

**Risk.** Do not claim species recovery. Document who pays for a false positive.

---

### 4. Glass-strike shutter

**Situation.** Campus glass kills small woodland birds at dawn. Stickers are ignored; buildings stay lethal.

**Non-human.** Silvereyes / spinebills / honeyeaters. Need: the pane to stop looking like a fly-through when they move.

- **Sense → act.** Lux (dawn) + mmWave in front of a demo pane → servo bead curtain / patterned shutter (W7). Default down at civil twilight.
- **AI API (W8).** Bird-vs-not image API and/or BirdNET; weather API for low sun/fog.
- **Enclosure.** Framed window analog. The shutter pattern **is** the ecology.
- **Demo.** Darken the room, wave a bird silhouette, shutter drops.

**Syllabus.** W1 calm ubicomp: the window is the interface. W2 implicit: time-of-day + approach, not a GUI.

**Vision Pro.** Volumetric “flight corridor” ghost toward the real pane (glass as sky). Pinch to replay a strike that did not happen. Gaze-dwell toggles what the bird sees vs what the human sees. **Best MR money-shot in the set.**

**If the headset dies.** Physical shutter still drops. Video still works.

**Risk.** Table-top pane is a model. Say so. Do not claim you retrofitted Abercrombie.

---

### 5. First-flush creek delay

**Situation.** Sydney storms flush oil, sediment, and dog-waste into Cooks / Wolli / Parramatta.

**Non-human.** Short-finned eel (*Anguilla australis*), freshwater shrimp, riparian plants. Need: dirty first minutes held back.

- **Sense → act.** Rain analog + turbidity (optical, W4) → servo weir flap (W7). First N mm → dirty cistern; later → “creek” tray.
- **AI API (W8).** BoM radar/nowcast so the flap pre-closes.
- **Enclosure.** Sandstone-coloured printed weir. A creek step, not a gadget.
- **Demo.** Two jugs: tea+glitter vs clean water.

**Syllabus.** W2 states: dry / first-flush / clean-flow / settle. Cite Country/water literature; **do not costume Aboriginal knowledge**. Tangible water is Week 9 made literal.

**Vision Pro.** Suspended “dirt” particles only in the first-flush volume. Scrub-gesture through one storm. Gaze on dirty cistern vs creek tray. Invisible pollution is the reason to wear a headset.

**If the headset dies.** Flap and two trays still tell the story.

**Risk.** Tiny volumes only. No real stormwater install this semester.

---

### 6. Noisy-miner clutter baffles

**Situation.** Noisy miners (*Manorina melanocephala*) dominate open plantings and exclude small insectivores.

**Non-human.** Superb fairywren / eastern spinebill. Need: **visual clutter**, not more noise.

- **Sense → act.** Mic (analogue/I2S) + lux → servo “shrub” vanes (W7). **No anti-miner speakers.**
- **AI API (W8).** BirdNET-class sound API: miner vs magpie vs wind. BoM wind so vanes do not fight a gale.
- **Enclosure.** Planter-scale thicket (laser-cut + PETG leaves). The device is a bush.
- **Demo.** Play miner chorus into a **closed box** (do not attract real miners on campus).

**Syllabus.** W2 implicit audio context. W6 BLE from a listen node to an actuate node if ADC2 vs WiFi fights you (Tutorial 6). Do not invent mid-air commands for wrens — they do not owe you a gesture set.

**Vision Pro.** Miner acoustic field as a spatial volume around the planter. Point-at-vane: clutter vs park. Gaze highlights the small-bird pocket the baffles create.

**If the headset dies.** Vanes still move to chorus vs rain vs silence.

**Risk.** Campus playback can attract miners. Headphones into the mic, or a closed box.

---

### 7. Honeyeater bath that is not a cat bowl

**Situation.** Heatwave baths help honeyeaters and also make a sit-and-wait perch for cats.

**Non-human.** New Holland honeyeater / little wattlebird: cool shallow water. Need: not a cat table.

- **Sense → act.** DS18B20 water temp + BH1750 + mmWave at rim-height → lid/raise (W7) on a cat-sized dusk signature. Recirculation pump when water is hot (PWM, W4).
- **AI API (W8).** Image API: cat vs bird vs empty. Weather API fills before 10 am on heatwave days.
- **Enclosure.** Stone-look bowl on a thin pedestal (hard for a cat to sit on).
- **Demo.** Hot water vs cool; stuffed cat on the rim.

**Syllabus.** W2 two desired states in conflict (bird drink vs cat hunt) — write ESD **twice**. W10 optional haptic band for the human if a cat is on the rim. Animals do not wear haptics.

**Vision Pro.** Cat approach cone vs bird approach cone around the physical bowl. Pinch to compare noon vs dusk. Gaze on the water shows temperature false-colour.

**If the headset dies.** Lid and pump still run. Manual: empty-and-clean (mosquitoes).

**Risk.** No live-cat tests. Standing water must be emptied.

---

### 8. Harbour silt valve (oyster tray)

**Situation.** After rain, Harbour turbidity and freshwater pulses stress Sydney rock oysters.

**Non-human.** Sydney rock oyster (*Saccostrea glomerata*). Need: less silt on the feeding window.

- **Sense → act.** Turbidity + water temp in a 10 L tank (isolate from salt) → flap or bubble stone (W7) after a simulated storm.
- **AI API (W8).** BoM rain nowcast. Skip satellite colour — overkill for Week 13.
- **Enclosure.** Mini oyster rack, timber + PETG (harbour material language).
- **Demo.** Pour silt water; valve closes; clear water; opens.

**Syllabus.** W1: the harbour is already a pervasive system; you add one calm valve. W9: the rack is the tangible. Do not dunk the ESP32.

**Vision Pro.** Silt as a volume in the tank, registered to the rack. Scrub one rain pulse. Gaze on closed vs open flap. MR shows what oysters filter; motors do the analog.

**If the headset dies.** Tank demo still reads from three metres away.

**Risk.** Salt kills electronics. No harbour deployment this semester.

---

## 4. Pick in 24 hours

| If you want… | Pick |
| --- | --- |
| Strongest mixed-reality overlay (things you cannot see) | **4 glass-strike**, then 1 flying-fox heat, then 5 first-flush |
| Strongest artefact if visionOS slips | **2 mound**, 7 bath, 5 weir |
| Strongest Week 2 proxemics essay | **3 possum gate** |
| Whole syllabus + a reason to borrow a Vision Pro | **4 glass-strike shutter** as artefact, overlay as exhibit |

Do not rebuild the old path-light bollard. Do not make Vision Pro the assessed interface.
