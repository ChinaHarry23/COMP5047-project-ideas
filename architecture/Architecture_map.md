# Architecture map — Wildlife-First Adaptive Path Light

Attach **`Architecture_map.svg`** as a proposal figure. This page is the caption and reading guide.

## One-line architecture

World (bush, path, night) → sensors → ESP32-S3 fusion → LED + 180° blade. On-device only. No screen.

```
 WORLD          bush / bats          path / walker         night lux / clock
                    │                      │                      │
 SENSE         analog FD mic          LD2410 mmWave          BH1750 / VEML7700
               10–15 + 25–35 kHz      presence + range       + sunset cache
                    │                      │                      │
 FUSE               └────────── ESP32-S3 / ESP-IDF ───────────────┘
                               persist · fuse · six-state machine
                    │                      │                      │
 ACTUATE       MOSFET + 2200 K         MG90S 180° blade      underside amber
               path LED                bush shield           maintainer
                    │                      │                      │
 POWER         18650 + buck            USB on the bench      solar = stretch
 DEMO          recorded NSW playback into the analog front-end
```

## Data flow

| From | To | Signal | Used for |
|---|---|---|---|
| BH1750 / VEML7700 | MCU I2C | lux | night / dawn inhibit |
| LD2410 | MCU UART | presence, range | walker exception |
| Analog FD | MCU ADC | envelope in two bands | extra bush shielding |
| BoM (cached) | NVS | sunset / sunrise | `is_night` without a live radio |
| FSM | MOSFET | PWM duty | path light + floor |
| FSM | MG90S | pulse width | blade azimuth |
| FSM | amber LED | slow PWM | fault / quiet-too-long |
| Playback (demo) | FD input | recorded calls | Week 13 without a live bat |

## Trust boundary

Nothing in the habitat needs a network. Optional BLE is a **maintainer log dump**, not a user interface. If BLE is not up by W12, omit it.

## Files

| File | What it is |
|---|---|
| `Architecture_map.svg` | Figure for Word / PDF appendix |
| `Hardware_stack.md` | Parts, buses, pin map, order list |
| `Software_stack.md` | ESP-IDF layers, tasks, six states |
| `Architecture_map.html` | Browser view |
