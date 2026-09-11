# Software stack — Wildlife-First Adaptive Path Light

Firmware is **ESP-IDF**, on-device, no screen. One or two of the five own this path. TinyML / ESP-DSP FFT / BLE are upgrades after the six-state machine works with lux + mmWave + analog bat-band + playback.

## Stack

```
┌─────────────────────────────────────────────────────────────┐
│  Optional (after demo path works)                           │
│  TinyML call classifier · ESP-DSP FFT · BLE log dump        │
├─────────────────────────────────────────────────────────────┤
│  App: six-state machine + actuator policy                   │
│  night_idle · human_path · bat_shield · dawn_inhibit        │
│  person_leaving · maintainer_fault                          │
├─────────────────────────────────────────────────────────────┤
│  Context fusion                                             │
│  1 s presence persist · dual-band energy · pulse-rate gate  │
│  lux threshold · sunset cache (NVS) · illuminance floor     │
├─────────────────────────────────────────────────────────────┤
│  Drivers (ESP-IDF)                                          │
│  i2c (BH1750/VEML7700) · uart (LD2410) · adc (bat envelope) │
│  ledc PWM (path LED, servo, underside amber)                │
├─────────────────────────────────────────────────────────────┤
│  ESP-IDF · FreeRTOS · ESP32-S3                              │
└─────────────────────────────────────────────────────────────┘
```

Cloud / phone is **not** in this stack. Bureau of Meteorology sunrise/sunset is fetched **once** (or weekly) and cached in NVS. Atlas of Living Australia / iNaturalist are design evidence, not runtime.

## Tasks (FreeRTOS)

| Task | Period | Reads | Writes |
|---|---|---|---|
| `task_lux` | 200 ms | I2C lux | `ctx.lux_lux` |
| `task_mmwave` | 100 ms | UART frames | `ctx.present`, `ctx.range_m` |
| `task_batband` | 20 ms | ADC envelope | `ctx.band_lo`, `ctx.band_hi`, `ctx.pulse_ok` |
| `task_clock` | 1 s | NVS sunset + uptime | `ctx.is_night` |
| `task_fsm` | 50 ms | `ctx` | `cmd.led_duty`, `cmd.servo_deg`, `cmd.amber` |
| `task_actuate` | 20 ms | `cmd` | LEDC / GPIO |

`task_fsm` is the only place policy lives. Drivers do not fade the LED on their own.

## Context struct (fusion inputs)

```
lux_lux          night if below L_NIGHT; inhibit LED if above L_SPILL
present          true only after 1 s continuous mmWave inside range gate (~5 m)
range_m          reject far clutter / vegetation where possible
band_lo          energy in ~10–15 kHz  (A. australis search)
band_hi          energy in ~25–35 kHz  (C. gouldii)
pulse_ok         pulse-rate in a bat-like window (reject wind rumble)
is_night         BoM cached sunset–sunrise
fault            lux/mmWave/ADC timeout or battery low
bat_quiet_too_long   optional maintainer hint, not a wildlife claim
```

## Six-state machine

```
                    lux > L_SPILL
         ┌──────────────────────────────► DAWN_INHIBIT
         │                                    │
         │  lux drops                         │ lux stays high
         ▼                                    ▼
   NIGHT_IDLE ◄── fade 5 s ── PERSON_LEAVING
         │ present 1 s              ▲
         ▼                          │ present clears
   HUMAN_PATH ── bat pulse_ok ──► BAT_SHIELD
         ▲                          │
         └──── 20 s since last pulse ┘
         
   any state + fault ──► MAINTAINER_FAULT (underside amber only)
```

| State | LED | Blade | Rule |
|---|---|---|---|
| `NIGHT_IDLE` | off | closed toward bush | Default. Darkness is the product. |
| `HUMAN_PATH` | fade up 1.5 s to path level ≥ floor | still bush-closed | Walker ~5 m. |
| `BAT_SHIELD` | may dim, **never below floor** | rotate to occlude bush / tighten on path | Dual-band + pulse-rate. Hold 20 s after last pulse. |
| `PERSON_LEAVING` | fade down 5 s | return toward bush | Presence cleared. |
| `DAWN_INHIBIT` | off | park | Do not compete with daylight or street spill. |
| `MAINTAINER_FAULT` | off (or floor if a person is still there) | park | Underside amber pulse. |

Servo must **not** start a move while `task_batband` is in a detection window (motor artefact).

## Demo vs upgrade

| Must work in Week 13 | Upgrade only |
|---|---|
| Lux inhibit, mmWave persist, analog dual-band threshold, six states, PWM fade, servo, playback stimulus | TinyML species classifier, ESP-DSP FFT at 96 kHz, BLE log, solar charge FSM |

False positives are **safe**: extra shielding. False negatives (missed call) are acceptable for the demo if playback still triggers `BAT_SHIELD`.

## Repo layout (suggested)

```
firmware/
  main/
    app_main.c
    ctx.h
    fsm.c / fsm.h
    drivers/lux.c  mmwave.c  batband.c  led.c  servo.c
    sunset_nvs.c
  CMakeLists.txt
  sdkconfig.defaults
```

Language: C (ESP-IDF). Keep Arduino out of the submitted firmware so the stack matches the proposal.
