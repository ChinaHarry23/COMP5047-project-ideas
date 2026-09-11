# COMP4447 / COMP5047 Project Proposal

**Wildlife-First Adaptive Path Light**

## Team members

| Name | Email | Student ID | Project manager? |
|---|---|---|---|
| Xinlong Bao | xbao0618@uni.sydney.edu.au | 550048909 | ☐ |
| Tianqi Wang | twan0795@uni.sydney.edu.au | 550216999 | ☐ |
| | | | ☐ |
| | | | ☐ |
| | | | ☐ |

*Five USYD Master of Computer Science students. Tick exactly one PM. Fill the three empty rows (name, unikey@uni.sydney.edu.au, SID) before submit. Proposed split (swap as a group): hardware/CAD/enclosure; ESP-IDF firmware; analog bat + lux/mmWave; trials/video/report; optional visionOS exhibit or PM/evidence. Shared: order, integrate, Week 13. Full group size does not reduce the bollard/enclosure/sensor scope.*

## Challenge statement

Artificial light at night (ALAN) is among the fastest-growing pollutants globally (skyglow ~9.6%/year; Kyba et al. 2023). Standard path lighting treats darkness as a human hazard to eliminate, and wildlife impact as an externality. For nocturnal species that share remnant bushland edges with people, darkness is habitat: commuting space, foraging space, and the insect prey base that insectivorous microbats depend on.

We address one local situation: a bushland-edge pedestrian path in a Sydney urban park. Across Greater Sydney, light at the forest edge is an anthropogenic barrier. Clutter-adapted microbats — notably Gould’s long-eared bat (*Nyctophilus gouldi*), plus chocolate wattled bat (*Chalinolobus morio*) and little forest bat (*Vespadelus vulturnus*) — are less active at lit edges than in dark interiors, and *N. gouldi* is almost confined to remnant bushland (Threlfall et al. 2013; Haddock et al. 2019a). Faster open-space species such as Gould’s wattled bat (*C. gouldii*) and the white-striped free-tailed bat (*Austronomus australis*) still use lit edges; that is not a licence to light. Cool-white and even filtered-white LEDs pull flying insects out of vegetation **without** a matching increase in bat feeding (Robert et al. 2025). National guidance is therefore: keep connecting corridors dark; if a person must walk, use low, warm, downward, shielded light, on only while they are there (DCCEEW 2023).

## Non-human stakeholder analysis

**Primary — Gould’s long-eared bat (*N. gouldi*) and other clutter-adapted Sydney edge species.** Radio-tracked *N. gouldi* in Cumberland State Forest barely enter the urban matrix; lit gaps inside remnant bush suppress *Nyctophilus* activity (Threlfall et al. 2013). Haddock et al. (2019a) found the same pattern at 31 Greater Sydney forest edges for *Nyctophilus* spp., *C. morio* and *V. vulturnus*. Need: a dark, glare-free vegetation corridor.

**Secondary — co-occurring open-space microbats (*C. gouldii*, *A. australis*).** Light-tolerant at Sydney edges (Haddock et al. 2019a); *C. gouldii* shows no short-term melatonin disruption under several LED spectra (Dimovski et al. 2023). They still do not gain a feeding benefit when LEDs vacuum insects (Robert et al. 2025). Need: dark intervals and no cool-white prey trap. Their louder calls are the **acoustic proxy** that the corridor is in use (*C. gouldii* ~25–34 kHz; *A. australis* search calls ~10–15 kHz, often audible — Pennay et al.).

**Tertiary — nocturnal moths and other flying invertebrates.** White LEDs are an ecological trap; a large fraction of insects at lamps die (Owens & Lewis 2018; Owens et al. 2020). Need: not an always-on cool-white beacon.

The system does not claim to know what a bat wants (Mancini 2016). It operationalises DCCEEW / EUROBATS consensus — less light, warmer spectrum, shorter duration, directional shielding, on only when a person is present — as default behaviour.

## Project idea

**Context of use.** A low bollard on the walkable side of a bushland-edge path. Default: off. Sensing runs continuously at low power. There is no screen, app, or explicit human UI. The interaction is ambient: light appears, a 180° blade shields the bush, and both withdraw by themselves.

**How the system behaves**

1. **Night, no person.** Ambient lux confirms dark; mmWave presence is clear. LED off. Blade closed toward the bush.
2. **Person approaching (~5 m).** LED fades up over ~1.5 s to a low 2200 K / PC-amber output on the path only. A measured path-illuminance floor is held so a pedestrian can still see the ground.
3. **Bat-band activity while a person is present.** Dual-band energy (~10–15 kHz or ~25–35 kHz) exceeds threshold at a minimum pulse rate. The blade tightens onto the path; the LED may dim but never below the human-safety floor. Hold 20 s after the last detection.
4. **Person leaves.** LED fades off over ~5 s. Return to (1).
5. **Dawn or existing spill.** Ambient lux above threshold. LED stays disabled.
6. **Maintenance.** Low battery, sensor fault, or a long gap in bat-band activity: slow-pulsing amber on the *underside* of the fixture only.

**Context recognition.** Four on-device signals — ambient lux, human presence/range, bat-band sound, time-of-night (BoM sunrise/sunset, cached) — fused in a rule-based ESP-IDF state machine on an ESP32-S3. The analog frequency-division front-end must pass **two** bands: ~10–15 kHz (*A. australis*) and ~25–35 kHz (*C. gouldii*). *Nyctophilus* calls are faint steep FM; we do not claim live species-ID. TinyML is an upgrade, not the demo path. BLE, if used, is maintainer logs only.

## Hardware / software architecture

On-device only: world → sense → fuse → actuate. No screen. Full diagrams: `architecture/Architecture_map.svg`, `Hardware_stack.md`, `Software_stack.md` (attach the SVG).

**Architecture map.** Bush/bats, path/walker, and night lux feed three sensors (analog frequency-division mic, LD2410, BH1750/VEML7700) plus a cached sunset. An ESP32-S3 running ESP-IDF persists, fuses, and runs a six-state machine, then drives a 2200 K path LED, an MG90S 180° bush blade, and an underside amber maintainer LED. USB/18650 powers the bench build; recorded NSW playback is the Week 13 stimulus.

**Hardware stack**

| Layer | Parts | Bus |
|---|---|---|
| Enclosure | PETG bollard, downward hood, printed 180° blade | — |
| Power | USB bench, or 18650 + buck (solar = stretch) | 5 V / 3.3 V |
| Compute | ESP32-S3 | — |
| Sense | BH1750/VEML7700; LD2410; MEMS mic + analog FD (gates ~10–15 kHz and ~25–35 kHz) | I2C; UART; ADC |
| Actuate | MOSFET + 2200 K / PC-amber LED; MG90S blade; underside amber | LEDC PWM / GPIO |
| Demo | Playback source into the analog front-end | — |

**Software stack (ESP-IDF / FreeRTOS)** — drivers (I2C, UART, ADC, LEDC) → fusion (1 s presence persist, dual-band energy, pulse-rate, lux, sunset cache, illuminance floor) → six-state policy (`night_idle`, `human_path`, `bat_shield`, `person_leaving`, `dawn_inhibit`, `maintainer_fault`) → PWM/servo/GPIO. TinyML, ESP-DSP FFT, and BLE are upgrades after this path works. No phone UI.

## Parts required

**Hardware.** ESP32-S3; BH1750 or VEML7700; LD2410; MEMS ultrasonic mic plus analog frequency-division module into ADC (not a from-scratch 192 kHz design); 2200 K / PC-amber LED + MOSFET; MG90S + printed 180° blade; underside amber LED; 18650 + buck; USB bench power; playback source. Solar is stretch.

**Software / APIs.** ESP-IDF state machine and drivers. Cached BoM sunrise/sunset. ALA / iNaturalist for the species list (evidence, not runtime). Optional later: ESP-DSP FFT, TinyML, BLE log dump.

**Enclosure.** Fusion 360, FDM PETG bollard with downward hood, printed 180° blade. Form: a shrouded low-glare path light.

## Timeline

Proposed split (swap as a group of five): hardware/CAD/enclosure; ESP-IDF; analog bat + sensors; trials/docs/video; optional visionOS exhibit or PM/evidence. Shared: order, integrate, presentation. **W4** submit and order. **W5–6** lux + mmWave; analog bat front-end + thresholding. **W6–8** LED + 180° blade and CAD v1; fusion state machine. **W9** mid-project check-in (after the mid-semester break). **W10–11** print, gasket, integrate, campus dusk trials with playback. **W11–12** report and manual. **W12** video. **W13** presentation. Vision Pro, if used, is an empathy exhibit beside the bollard — not the assessed product.

## Potential risks

**Ultrasound.** Wind, insects, and servo noise can look like calls. Mitigation: dual-band gates (~10–15 and ~25–35 kHz) + pulse-rate; false positives only add shielding; servo does not move in a detection window; the demo uses recorded NSW playback (Pennay-style libraries). We do **not** depend on 192 kHz I2S FFT — analog frequency-division into the ADC is the path that must work. We do not claim to detect live *Nyctophilus*.

**Presence vs safety.** mmWave may trigger on dogs, possums, or vegetation (~1 s persistence + range gate). Shielding/dimming must not make the path unwalkable (hard illuminance floor). Briefly lighting a non-human body is preferable to leaving a person unlit.

**Scope and access.** Five people still owe a fabricated enclosure, working features, report, and video; extra members parallelise work, they do not drop the hardware artefact. Night park access needs permission. Mitigation: order parts in W4; campus dusk trials first; gasketed PETG bollard; document outdoor v2 (solar, IP rating) in the report rather than calling weatherproofing “out of scope.”

**Theory.** Every actuation (spectrum, blade, duration, default-off) will be justified against a named non-human need (ACI / multispecies design). We will not claim a one-semester population increase.

## Appendix — attach, do not skip

1. Bollard sketch: downward path beam vs bush side blocked by the 180° blade.
2. State-machine diagram of the six behaviours (also in `Software_stack.md`).
3. Spectral note: 4000 K white vs 2200 K / PC-amber (DCCEEW; Robert et al. 2025, amber λP 601 nm).
4. **Architecture map** — `architecture/Architecture_map.svg` (hardware + software stack).

**Selected references.** Threlfall et al. (2013) *Austral Ecology* 38:921–930. Haddock et al. (2019a) *Biol. Conserv.* 236:17–28; (2019b) *Austral Ecology*. Robert et al. (2025) *Ecosphere* 16:e70291. Dimovski et al. (2023) *R. Soc. Open Sci.* 10:221436. DCCEEW (2023) *National Light Pollution Guidelines for Wildlife*. Kyba et al. (2023) *Science* 379:265–268. Owens & Lewis (2018) *Ecol. Evol.* 8:11337–11358. Voigt et al. (2018) EUROBATS Series 8. Mancini (2016) *Int. J. Hum.-Comput. Stud.* 98:221–233. Full annotated list: `Evidence_pack.md`.

---
