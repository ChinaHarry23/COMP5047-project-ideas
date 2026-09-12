# Software stack — Catchment bin (both approaches)

Firmware is **ESP-IDF**, C, on-device, no screen. One or two of the five own this path. visionOS is a **separate repo folder**, started after the W9 physical test.

---

## Shared layers

```
┌─────────────────────────────────────────────────────────────┐
│  Optional after demo path works                             │
│  visionOS overlay · BLE log · TinyML on-device class        │
├─────────────────────────────────────────────────────────────┤
│  App: ONE state machine (A or B) + actuator policy          │
│  Policy only in task_fsm                                    │
├─────────────────────────────────────────────────────────────┤
│  Context fusion                                             │
│  persist · hysteresis · high_risk · bird_class · api_ok     │
│  NVS: last forecast, last class, sunset                     │
├─────────────────────────────────────────────────────────────┤
│  Drivers                                                    │
│  i2c · uart · adc · ledc · gpio · wifi sta                  │
├─────────────────────────────────────────────────────────────┤
│  ESP-IDF · FreeRTOS · ESP32-S3                              │
└─────────────────────────────────────────────────────────────┘
```

Cloud / phone is **not** in this stack. Roman / ALA / DCCEEW are evidence. ChatGPT is not the Credit API.

---

## Shared tasks

| Task | Period | Writes |
|---|---|---|
| `task_lux` | 200 ms | `ctx.lux` |
| `task_analog` | 50–200 ms | `ctx.turbidity`, `ctx.rain` |
| `task_radar` | 100 ms | `ctx.present`, `ctx.range_m` |
| `task_classify` | 2–5 s | `ctx.class_id`, `ctx.who`, `ctx.api_ok` |
| `task_api_bom` | 5–10 min | `ctx.forecast_rain` |
| `task_fsm` | 50 ms | `cmd.lid`, `cmd.weir`, `cmd.flag`, `cmd.piezo`, `cmd.amber` |
| `task_actuate` | 20 ms | LEDC / GPIO; `ctx.motor_busy` |

`task_fsm` is the only place justice lives.

---

## Shared `ctx`

```
api_ok            false → approach fail-* row
high_risk         balloon | soft_plastic
bird_class        rim present + low ToF  (W8: image who==gull/ibis)
human_in_gap      !gap_clear || who==person
forecast_rain     BoM nowcast, cached
turbidity         ADC1
fault             timeout, brown-out, stall
motor_busy        discard ADC
piezo_left        3 → 0 then mute
```

---

## Approach 1 FSM (weir)

See `Approach_A_VR.md`. States: `DRY` `PRE_STORM` `FIRST_FLUSH` `CLEAN` `SETTLE` `FAULT`.  
Fail: weir to **HOLD**. High-risk keeps HOLD even if water looks clear.

```
                    forecast or rain
         DRY ──────────────────────────► PRE_STORM
          ▲                                 │
          │                                 │ dirty or high_risk
          │                                 ▼
       SETTLE ◄── rain ends ── FIRST_FLUSH ── clear+not high_risk ──► CLEAN
                                              │
         any + fault ──► FAULT (HOLD + amber)
```

---

## Approach 2 FSM (lid)

See `Approach_B_bin_guard.md`. States: `OPEN_IDLE` `LID_SHUT` `HUMAN_SAFE` `FAIL_SHUT` `FAULT` `SERVICE`.

```
   OPEN_IDLE ── high_risk AND bird_class AND gap_clear ──► LID_SHUT
       ▲                                                       │
       └── bird gone 8 s, or SERVICE ──────────────────────────┘

   !gap_clear or who==person ──► HUMAN_SAFE (open/freeze)
   api_ok==false ∧ bird_class ──► FAIL_SHUT
   fault ──► FAULT (shut + amber, no siren)
```

Piezo: compiled out by default. If on, one-shot in the `LID_SHUT` entry, then mute.

---

## Suggested repo layout

```
firmware/
  main/
    app_main.c
    ctx.h
    fsm_a.c / fsm_b.c      ← select with Kconfig
    drivers/lux.c radar.c analog.c cam_http.c servo.c piezo.c
    nvs_cache.c
  CMakeLists.txt
  sdkconfig.defaults
cad/
docs/
visionos/                  ← after Week 9 only
```

Language: C (ESP-IDF). Keep Arduino out of the submitted firmware.

---

## Demo vs upgrade

| Must work Week 13 | Upgrade only |
|---|---|
| Turbidity **or** lid threshold, six states, one servo, radio-down fail-*, stuffed/jug stimulus | On-device TinyML, BLE log, piezo, full visionOS |
| False **hold/shut** is safe | False **creek/open** on a balloon is the residual — say it |

Headset code must not link against `cmd.*`.
