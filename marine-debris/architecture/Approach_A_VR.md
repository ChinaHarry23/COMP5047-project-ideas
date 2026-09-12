---
course: COMP4447 / COMP5047
title: "Approach 1 — ESP32 catchment bin + immersive VR inquiry"
updated: 2026-09-12
---

# Approach 1 — Immersive awareness (VR second station)

Pass artefact = the **bin**. VR / Apple Vision Pro = **inquiry**. If the headset does not boot in Week 13, two trays and a weir still show first-flush litter. Gaze, pinch, and dwell **never** fire a servo.

This is the architecture that matches Weeks 10–12 of the unit (wearable / gaze / mixed reality) without failing the “no screen-primary UI” Pass rule.

---

## 1. What this approach is for

Humans cannot see a prion’s proventriculus–gizzard isthmus, and they cannot see a balloon become marine debris three suburbs downstream. Approach 1 makes that **legible** next to a working catchment analog.

| Actor | What they get |
|---|---|
| Short-tailed shearwater (named, downstream) | High-risk items held in the “cistern” tray instead of the “creek” tray |
| Waterway | First dirty millimetres visible as a volume, not a slogan |
| Human visitor | Headset overlay: gut, dose–response curve, balloon vs fragment |
| Headset | Must not be required for the mid-check or the Pass demo |

---

## 2. ESD (write these in the report)

**ESD 1 — shearwater (care, in the enclosure)**

| | |
|---|---|
| Expected | Forage at sea without balloons in the first-flush pulse |
| Sensed | Rain / turbidity / image class of what sits on the weir |
| Desired | High-risk litter stays in the hold tray; flap does not dump it to “creek” |

**ESD 2 — human (inquiry, on the headset only)**

| | |
|---|---|
| Expected | Does not know what one balloon does in a Procellariiform gut |
| Sensed | Gaze-dwell on the physical tray; pinch on a timeline |
| Desired | Sees Roman’s 20.4% / 9 / 93 **as a model**, labelled as such |

Do not write ESD for “the VR user saves the species.”

---

## 3. One-line architecture

```
WORLD     rain / first flush         litter on the weir           forecast
              │                           │                          │
SENSE      analog turbidity            camera → image API         WiFi BoM
           + rain proxy                (balloon / soft / other)
              │                           │                          │
FUSE                    ESP32-S3 / ESP-IDF   six-state
              │                           │
ACTUATE    MG90S weir flap              MG90S human flag           amber
              │
INQUIRY    Vision Pro overlay registered to the two trays
           (gaze reveals; pinch scrubs; never actuates)
DEMO       jug of tea+glitter + a balloon vs tap water
```

---

## 4. Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| LED + photodiode across the channel | ADC1 GPIO 1 | turbidity | dirty vs clear |
| Soil-moisture or tip-bucket | ADC1 GPIO 2 or GPIO interrupt | raining, rain_mm | start of storm |
| BH1750 | I2C 8/9 | lux | day demo vs night park |
| Camera / HTTP | WiFi | class: balloon, soft_plastic, hard, food, empty | Credit; high-risk bit |
| BoM / Open-Meteo | NVS | rain_next_60m | pre-close weir to hold |
| FSM | servo GPIO 7 | weir: hold vs creek | water is the display |
| FSM | servo GPIO 11 | flag up = “this pulse is lethal analog” | human, not wildlife |
| FSM | amber GPIO 5 | fault | underside only |
| visionOS | visitor | volumes, labels | **read-only** |

---

## 5. Hardware (Approach 1 delta)

Shared MCU / power / PETG body: see `Hardware_stack.md`.

| Extra | Why |
|---|---|
| Second printed tray (“creek”) | First-flush story readable at 3 m |
| Weir MG90S + gasketed channel | Non-screen actuation (Pass) |
| Human flag MG90S | Tangible “this is a dirty pulse” |
| ESP32-S3-CAM or OV2640 | Image API for balloon vs food |
| Vision Pro (borrow) | W10–12 only, after W9 |

**Do not buy** a second compute stick “for VR.” The headset is a viewer. Do not buy a phone-app dashboard.

---

## 6. Software

Tasks: `task_turb` 200 ms · `task_rain` · `task_lux` · `task_classify` 2–5 s · `task_bom` 5–10 min · `task_fsm` 50 ms · `task_actuate` 20 ms.

Extra `ctx`: `turbidity`, `raining`, `rain_mm`, `class_id`, `high_risk`, `api_ok`, `forecast_rain`.

```
   DRY
     │ rain start OR forecast_rain
     ▼
   PRE_STORM (weir to HOLD)
     │ turbidity high OR high_risk
     ▼
   FIRST_FLUSH (HOLD tray; flag UP)
     │ N mm passed AND turbidity low AND NOT high_risk
     ▼
   CLEAN (creek tray; flag down)
     │ rain ends
     ▼
   SETTLE → DRY

   api_ok false ── still sort on turbidity / rain_mm
                   never dump HOLD because WiFi died
   fault ──► FAULT (weir to HOLD, amber)
```

| State | Weir | Flag | Rule |
|---|---|---|---|
| `DRY` | park / hold-ready | down | Default |
| `PRE_STORM` | hold | down | Feedforward from BoM |
| `FIRST_FLUSH` | **hold** | **up** | Dirty or high-risk |
| `CLEAN` | creek | down | Later, clearer water |
| `SETTLE` | hold | down | Cistern rests |
| `FAULT` | **hold** | down | Fail-protective for the “creek” |

Hysteresis: glitter settling must not chatter the flap. High-risk image keeps `FIRST_FLUSH` even if turbidity drops (a balloon in clear water is still the Roman item).

---

## 7. Vision Pro / VR exhibit (after Week 9)

| Week | Headset job |
|---|---|
| 10 | Wearable: visitor wears the headset; **nothing on a bird** |
| 11 | Gaze-dwell 0.8 s on the hold tray → gut-isthmus ghost + “balloon 32×” |
| 12 | Mixed reality: particles only in the first-flush volume; pinch scrubs 0–93 items with the caption **model, not a count** |
| 13 | Optional second station. Physical trays first |

**Hard rules**

- Gaze / pinch / voice do **not** write `cmd.*`.
- If AVP is unavailable: a **recorded** fly-through video beside the bin — not a website as the product.
- Do not show a live ChatGPT panel as the Credit API. Credit API = image class + BoM.

Suggested visionOS objects (RealityKit, registered to printed fiducials on the trays):

1. False-colour “water” only above the hold tray during `FIRST_FLUSH`.
2. A single balloon mesh that lodges in a schematic gizzard when the image class is balloon.
3. A slim curve widget: 1 item → 20.4%, 9 → 50%, 93 → 100%, sourced from Roman 2019 Fig. 4, footnoted.

---

## 8. Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Serial: flap dumps jug A to hold, jug B to creek | 3 |
| 8 | JPEG stub “balloon” keeps flap on hold; BoM rain pre-moves; WiFi down still uses turbidity | 2 |
| 9 | Tea+glitter → hold; tap → creek; balloon analog stays in hold (**mid-check, no headset**) | 1+2 |
| 10–11 | Print sandstone bin, hide servos | 3 |
| 11–12 | Overlay registered to trays; gaze does not move flap | 5 |
| 13 | 3 min physical; 1 min headset optional; “not the Tasman” | 4 |

**Week 13 script (Approach 1).** (1) Dry trays. (2) Pour tea+glitter → hold, flag up. (3) Pour tap → creek. (4) Place balloon analog → stays in hold even in clear water. (5) Unplug WiFi → still holds dirty. (6) Optional: headset, pinch the curve, take headset off, flap still works.

---

## 9. Risks specific to Approach 1

- VR swallows the five-person week. **Ban headset work until W9 is green.**
- Visitors think the product is the headset. Opening sentence of the talk: “The artefact is the bin.”
- Over-claiming the 93-item number. Overlay must say **model**.
