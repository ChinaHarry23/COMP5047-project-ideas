# COMP4447 / COMP5047 Project Proposal

**Catchment Bin for Plastic-Ingesting Seabirds**

## Team members

| Name | Email | Student ID | Project manager? |
|---|---|---|---|
| Xinlong Bao | xbao0618@uni.sydney.edu.au | 550048909 | ☐ |
| Tianqi Wang | twan0795@uni.sydney.edu.au | 550216999 | ☐ |
| | | | ☐ |
| | | | ☐ |
| | | | ☐ |

*Five USYD Master of Computer Science students. Tick exactly one PM. Fill the three empty rows before submit. Proposed split (swap as a group): hardware/CAD/enclosure; ESP-IDF FSM; sensors + analog turbidity; evidence/video; visionOS **after** Week 9. Extra members do not shrink the physical bin.*

## Challenge statement

Injury and fatality from **ingestion of, or entanglement in, harmful marine debris** is an EPBC Key Threatening Process (listed 2003; Threat Abatement Plan 2018). It is not a studio invention. CSIRO / UTAS necropsied **1,733 Procellariiform seabirds of 51 species** collected in Australia and New Zealand: **557 (32.1%)** had ingested debris; **13** were confirmed to have died from it; gastrointestinal obstruction was the leading mechanism. A dose–response model estimates **~20.4% lifetime mortality from a single item**, 50% at nine items, and 100% at 93 items — a fitted curve, not a field count (Roman, Hardesty, Hindell & Wilcox 2019, *Scientific Reports*). Balloons were about **32×** more likely to kill than a hard plastic fragment. Wilcox, Van Sebille & Hardesty (2015, *PNAS*) locate the **highest expected adverse effects in Australasia**, at the Tasman Sea / Southern Ocean boundary. Short-tailed shearwaters (*Ardenna tenuirostris*) that breed in NSW / Tasmania / Victoria routinely return with plastic loads; beachcast fledglings at Phillip Island and in Tasmania show very high ingestion rates.

We address one **Sydney** situation: litter at campus and inner-city bins is pulled out by silver gulls and Australian white ibis, then washed by first-flush stormwater into Cooks / Harbour systems that connect to the Tasman foraging range of those shearwaters. A 13-week ESP32 artefact cannot clean the Tasman Sea. It **can** embody the documented pathway — bin → water → gut — as a campus analog, and say what it is not.

## Non-human stakeholder analysis

**Primary — short-tailed shearwater (*Ardenna tenuirostris*) and other plastic-ingesting Procellariiformes (fairy prion *Pachyptila turtur* was among Roman’s confirmed deaths).** Need: fewer balloons and soft plastics entering the sea; they do **not** forage at Camperdown bins. The bin is a catchment analog, not a nest box.

**Secondary — silver gull (*Chroicocephalus novaehollandiae*) and Australian white ibis (*Threskiornis molucca*).** They are the **vector** at the bin: they open bags, scatter high-risk items, and move litter toward drains. They are not the EPBC victim and not a pest to punish. Need: the aperture closed when high-risk debris is exposed so they cannot pull a balloon into the gutter. No ultrasound (it hits other wildlife).

**Tertiary — the waterway (Cooks / Harbour first flush).** Need: dirty first millimetres held, not costume Country. Cite CRVA / EPA stormwater as the mechanism, not a listed eel we cannot name.

The system does not claim to know what a shearwater wants (Mancini 2016). It operationalises the TAP and Roman’s high-risk-item finding as default behaviour: **hold balloons and soft plastic; do not leave an open buffet; do not siren the habitat.**

## Project idea

**Context of use.** A sandstone-coloured PETG “catchment-mouth bin” on a demo boardwalk: two trays (held litter / “creek”), a lid, no screen, no phone UI. Sensing runs continuously. Two architectures share this object.

### Approach 1 — immersive inquiry (VR / Vision Pro as second station)

The **Pass product remains the bin**. Rain + turbidity + an image class (balloon / soft plastic / other) drive a weir flap and a human-facing flag. The headset, after Week 9, registers a gut / ocean volume to the physical trays: pinch to scrub the 1 / 9 / 93-item curve; gaze-dwell reveals “what this fragment does in a prion’s isthmus.” **Gaze must not fire the flap.** If the headset dies, the trays still tell the stormwater story.

### Approach 2 — bin guard (keep birds off high-risk litter)

When the image API says high-risk debris is exposed **and** mmWave / ToF says a bird-class body is at the rim, the lid **closes**. That is the warning: the aperture disappears. An optional piezo may click **once, short, audible, not ultrasonic**, then stay silent (habituation + ACI). A person with a hand in the gap: never pinch. If the classifier dies: **fail-closed** while a bird-class body is present and debris is still visible; fail-open for a human in the aperture. Live birds are never used; stuffed gull + balloon vs banana.

**Recommended Week 13 build:** one enclosure running Approach 2 as the care FSM, with Approach 1’s overlay as the inquiry station. Do not ship two boxes.

**Context recognition.** On-device: lux, rim presence/range, analog turbidity (and/or rain proxy), image class via WiFi (Credit API). ESP-IDF six-state machines (one per approach). BLE, if used, is a maintainer log only.

## Hardware / software architecture

World → sense → fuse → actuate. No screen in the Pass loop. Figures: `marine-debris/architecture/Architecture_A.svg`, `Architecture_B.svg`.

**Shared stack.** ESP32-S3 (or S3-CAM), ESP-IDF / FreeRTOS, C. BH1750 (I2C), LD2410 (UART), analog turbidity on **ADC1** (WiFi kills ADC2), MG90S lid and/or weir, underside amber. USB bench; 18650 is v2.

**Approach 1 extras.** Second tray + weir servo; BoM rain nowcast; visionOS overlay registered to the trays. Fail: flap to the **hold** tray.

**Approach 2 extras.** Rim ToF or mmWave; lid servo + gap end-stop; optional capped piezo. Fail: lid **closed** to birds, never closed on a human hand.

Policy lives only in `task_fsm`. Drivers do not start the lid.

## Parts required

**Hardware.** 2× ESP32-S3 (one CAM if possible); BH1750; LD2410C; analog turbidity (LED + photodiode); 2× MG90S; MOSFET; amber LED; PETG catchment-bin + two trays; stuffed gull; balloon analog + banana; 5 V 2 A USB. Optional: piezo, VL53L0X, 18650.

**Software / APIs.** ESP-IDF state machine. Image classifier (balloon/soft-plastic vs food vs empty; Approach 2 also gull vs person). BoM / Open-Meteo rain. Roman / ALA / DCCEEW = evidence, not runtime. Optional BLE log. visionOS after W9.

**Enclosure.** Fusion 360, FDM PETG. Form: a stormwater-mouth bin, not a gadget box.

## Timeline

**W7** lid or weir moves from a dummy flag; end-stop aborts close. **W8** image stub + BoM cached in NVS; radio-down follows the fail-* row. **W9** mid-check: one feature **without** a headset (tea+glitter → hold tray, **or** stuffed gull + balloon → lid). **W10–11** print v2, gasket. **W11–12** optional Vision Pro overlay (Approach 1) — gaze reveals, never actuates. **W12** 3–5 min video. **W13** physical demo first. Tutor must approve this pivot (proposal was due W4).

## Potential risks

**Honesty of the analog.** Shearwaters do not eat from campus bins. Mitigation: name the **vector** (gull/ibis) and the **downstream beneficiary** (shearwater); write two ESD tables; never claim a population recovery.

**Approach 2 as a scarecrow.** Continuous speakers and ultrasound fail the unit’s ACI ethics and hit non-target wildlife. Mitigation: the act is a **lid**; piezo is optional, ≤200 ms, ≤3 events then mute; no live-bird tests.

**Classifier error.** False “balloon” closes the lid (safe for the shearwater, annoying for a person). False “food” leaves a balloon exposed (ethical residual — say so). Pinch-risk: `gap_clear` freezes the servo.

**VR as product.** A headset-only project fails Pass. Mitigation: Week 9 feature is physical; headset is inquiry.

**Scope.** Five people still owe enclosure, FSM, report, video. Order this week; dummy trays before pretty CAD.

## Appendix — attach

1. Catchment-bin sketch: lid, two trays, stormwater mouth.  
2. State machines (Approach 1 and 2) — also in `Software_stack.md`.  
3. Roman 2019 dose–response note: 20.4% / 9 / 93 is a **model**; confirmed deaths were 13/1733.  
4. Architecture maps — `Architecture_A.svg`, `Architecture_B.svg`.

**Selected references.** Roman et al. (2019) *Sci. Rep.* 9:3202. Wilcox, Van Sebille & Hardesty (2015) *PNAS* 112:11899–11904. DCCEEW (2018) *TAP marine debris*; EPBC KTP 2003. Roman et al. (2016) *PLoS ONE* (eastern Australia avifauna). DCCEEW *Impacts of plastic debris on Australian marine wildlife*. Mancini (2016) *IJHCS* 98:221–233. Full list: `2_Evidence_pack.md`.
