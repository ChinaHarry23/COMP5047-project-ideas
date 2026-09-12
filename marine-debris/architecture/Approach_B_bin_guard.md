---
course: COMP4447 / COMP5047
title: "Approach 2 — Bin guard: detect high-risk litter, keep birds off"
updated: 2026-09-12
---

# Approach 2 — Garbage monitor that keeps birds off the bin

The brief you asked for: **detect “bad” things in / at a bin, then warn birds so they leave**. Under this unit’s rubric that cannot be an ultrasound scarecrow or a continuous speaker. Those hit non-target wildlife, fail ACI, and are the opposite of the marine-debris TAP (remove high-risk items, do not punish the vector).

**What we build instead:** the warning **is the aperture disappearing**. When high-risk debris is exposed and a bird-class body is at the rim, an MG90S **lid closes**. An optional piezo may click **once, short, audible (2–3 kHz), not ultrasonic**, then stay silent. Humans with a hand in the gap are never pinched.

Shearwaters still do not eat from this bin. Silver gull / ibis are the **vector**. Write two ESD tables.

---

## 1. Why a lid is the “repel,” not a siren

| Tempting product | Why it fails this course / the science |
|---|---|
| Ultrasonic “bird away” | Hits other taxa; not in the TAP; unit already forbade ultrasound on cats/miners |
| Continuous alarm at the rim | Pest-control aesthetic; habituation; campus noise; not ACI |
| App notification “bird on bin” | Screen-primary; Pass-illegal as the interface |
| **Lid + geometry** | Non-screen actuation; same grammar as the honeyeater bath / bait cover; justice is fail-closed |

If the group insists on an animal-facing sound: **one 200 ms click, maximum three events per approach, then mute for 10 minutes.** Document it as a stretch. The Pass demo must succeed with the **lid only**.

---

## 2. ESD (write both)

**ESD 1 — short-tailed shearwater (downstream beneficiary)**

| | |
|---|---|
| Expected | No balloon / soft plastic entering the first-flush pulse |
| Sensed | Image class of exposed debris + rain/turbidity (shared with A) |
| Desired | High-risk items stay **inside** a closed bin |

**ESD 2 — silver gull / ibis (vector, not a pest)**

| | |
|---|---|
| Expected | Open buffet on an overflowing bin |
| Sensed | mmWave / ToF bird-class at rim + high-risk class |
| Desired | Gap too small to pull a balloon; brief click optional |
| Who pays | The gull loses that meal. Say so. Do not add a second punishment. |

**ESD 3 — human hand (safety, not the user of the product)**

| | |
|---|---|
| Expected | Empties or stuffs the bin |
| Sensed | Large, slow body in the aperture (`gap_clear` false) |
| Desired | Servo frozen / opens. Never pinch. |

---

## 3. One-line architecture

```
WORLD     high-risk litter           bird-class body at rim         human hand
              │                            │                            │
SENSE      camera → image API          LD2410 + ToF                 end-stop / class human
              │                            │                            │
FUSE                    ESP32-S3  justice FSM
              │
ACTUATE    MG90S lid (primary)     optional piezo click (capped)    amber / flag
DEMO       stuffed gull + balloon vs banana; never a live bird
```

---

## 4. Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| Camera / HTTP | WiFi | `debris`: balloon, soft, hard, food, empty | high_risk = balloon \| soft |
| LD2410 | UART | present, range | 1 s persist; rim gate ~0.3–0.8 m |
| VL53L0X or second ToF | I2C | height / low profile | bird-class vs person (person is taller) |
| BH1750 | I2C | lux | day (gull/ibis hours) vs night park |
| End-stop in lid throat | GPIO 12 | gap_clear | **never close on a body** |
| Optional image | WiFi | `who`: gull, ibis, person, empty | Credit; geometry still does most of it |
| FSM | MG90S GPIO 7 | lid shut / open | the actual “repel” |
| FSM | piezo GPIO 6 | ≤200 ms, ≤3 / approach | optional warn |
| FSM | amber GPIO 5 | fault | human maintainer |

---

## 5. Hardware (Approach 2 delta)

| Extra | Why |
|---|---|
| Lid + printed throat sized so a gull bill cannot drag a balloon out when shut | Geometry > classifier |
| LD2410 at rim height | Presence |
| VL53L0X looking across the mouth | Low body vs standing person |
| Gap end-stop | Pinch-risk |
| Optional piezo + MOSFET, **no ultrasonic transducer** | Capped warn |
| Stuffed / printed gull | Week 13 stimulus |

Shared CAM / BH1750 / PETG body: `Hardware_stack.md`. Two trays from Approach 1 can stay on the same print so one box runs both FSMs (`Kconfig` `APPROACH_A` / `APPROACH_B`).

---

## 6. Software

Tasks: `task_lux` · `task_radar` 100 ms · `task_tof` 50 ms · `task_classify` 2–5 s · `task_fsm` 50 ms · `task_actuate` 20 ms · `task_piezo` (one-shot).

Extra `ctx`: `high_risk`, `bird_class`, `human_in_gap`, `gap_clear`, `present_1s`, `piezo_left`, `api_ok`.

`bird_class` heuristic for W9 (no API): present within rim gate **and** ToF height below H_PERSON (~0.4–0.6 m on the demo stand). W8 image upgrades `who`.

```
   OPEN_IDLE
     │ high_risk AND bird_class AND gap_clear
     ▼
   LID_SHUT  (+ optional 1 click)
     │ bird_class clears for 8 s AND (not high_risk OR human service)
     ▼
   OPEN_IDLE

   HUMAN_IN_GAP (gap_clear == false) ──► freeze / OPEN   never pinch
   class person at rim ──► OPEN (person is emptying the bin)
   api_ok false AND bird_class ──► FAIL_SHUT
   api_ok false AND NOT bird_class ──► stay last pose
   fault ──► FAIL_SHUT + amber   (protective for downstream)
```

| State | Lid | Piezo | Rule |
|---|---|---|---|
| `OPEN_IDLE` | open | mute | Default; food-only bin stays open |
| `LID_SHUT` | **closed** | 0–1 click | High-risk + bird-class |
| `HUMAN_SAFE` | open / freeze | mute | Hand or person class |
| `FAIL_SHUT` | closed | mute | Classifier died while a bird-class body is there |
| `FAULT` | closed | mute | Amber; still no siren |
| `SERVICE` | open | mute | Magnet / button for emptying |

**Fail grammar.** Fail-**closed** to bird-class when the API is down and debris is still visibly in the analog (demo: balloon left on the rim). Fail-**open** for `human_in_gap`. Write who pays for a false shut (person annoyed) vs false open (balloon still available).

Piezo policy (if compiled in):

```
if entering LID_SHUT && piezo_left > 0:
    pulse 200 ms @ ~2.5 kHz
    piezo_left--
    mute_until = now + 10 min after third click
```

Never PWM a “distress call.” Never 20–80 kHz.

---

## 7. VR (optional on Approach 2)

Same overlay as Approach 1, registered to the **lid**. Gaze-dwell: “this aperture is what a gull uses to pull a balloon toward the drain.” Gaze does **not** shut the lid. If you only have time for one headset beat, spend it on Approach 1’s gut curve.

---

## 8. Implementation plan

| Week | Acceptance test | Who |
|---|---|---|
| 7 | Lid shut/open from serial; end-stop aborts close | 3 |
| 8 | HTTP stub: balloon+gull → shut; banana+gull → stay open; person → open | 2 |
| 9 | Stuffed gull + balloon → lid; stuffed gull + banana → open; hand in gap → freeze (**mid-check**) | 1+2 |
| 10–11 | Print lid throat; measure so a balloon cannot be pulled through when shut | 3 |
| 11–12 | Optional overlay | 5 |
| 13 | Three props (balloon, banana, stuffed gull); unplug WiFi + gull → shut; **no live birds** | 4 |

**Week 13 script (Approach 2).** (1) Open bin, banana on rim, stuffed gull → stays open. (2) Swap banana for balloon analog → lid shuts; say “the warning is the missing aperture.” (3) Hand in throat → lid opens / freezes. (4) Unplug WiFi, leave balloon, present gull → fail-shut. (5) Optional one click, then silence. (6) “Shearwaters are downstream; this is not a Manly colony gadget.”

---

## 9. Risks specific to Approach 2

- **Live birds.** Never. Ethics + useless (gulls habituate; shearwaters are not here).
- **Scarecrow scope creep.** If someone adds a siren “to make the demo pop,” remove it before the showcase.
- **Ibis / gull politics.** These birds are urban survivors, not the KTP victim. The report must not read as a council pest tender.
- **False open.** A missed balloon is the ethical residual for the shearwater. Prefer a slightly eager lid over a hungry rim.
- **False shut on a person.** End-stop + person class. Document in the manual: press SERVICE.
