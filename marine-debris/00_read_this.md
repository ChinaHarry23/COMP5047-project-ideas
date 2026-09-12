---
course: COMP4447 / COMP5047
title: "Catchment bin for plastic-ingesting seabirds — start here"
updated: 2026-09-12
lang: en
---

# Catchment bin for plastic-ingesting seabirds

> **中文**：[[00_read_this_cn]]
> Brief: *Designing for a More-Than-Human Australia*.
> Pivot from the 20-idea list onto a **source-backed** topic: EPBC Key Threatening Process *harmful marine debris* + Roman et al. 2019 (CSIRO / UTAS).

## Open in this order

| File | What it is |
|---|---|
| [1_Proposal.md](1_Proposal.md) | **Paste this** into Word / Google Docs (11 pt, 2 cm). Target 1–2 pages + appendix figures. |
| [1_Proposal_cn.md](1_Proposal_cn.md) | Same proposal in Chinese. |
| [2_Evidence_pack.md](2_Evidence_pack.md) | Roman 2019, EPBC KTP, Sydney catchment cites → design moves. |
| [architecture/Architecture_map.md](architecture/Architecture_map.md) | How to read the two maps. |
| [architecture/Approach_A_VR.md](architecture/Approach_A_VR.md) | Approach 1 — ESP32 bin + immersive VR inquiry. |
| [architecture/Approach_B_bin_guard.md](architecture/Approach_B_bin_guard.md) | Approach 2 — detect high-risk litter, keep birds off the bin. |
| [architecture/Hardware_stack.md](architecture/Hardware_stack.md) | Shared parts, pins, two BOM deltas. |
| [architecture/Software_stack.md](architecture/Software_stack.md) | Shared ESP-IDF layers + both FSMs. |
| [architecture/Hardware_BOM.md](architecture/Hardware_BOM.md) | Order list. |
| [architecture/Architecture_map.html](architecture/Architecture_map.html) | Browser view of both SVG figures. |

## Rubric rule (do not break)

Pass artefact = **ESP32-S3 + ≥2 sensor types + non-screen actuation + fabricated enclosure**.  
**VR / Vision Pro is a second station.** Gaze must not fire the lid, flap, or piezo. If the headset dies in Week 13, the bin still holds litter.

Approach 2 does **not** use ultrasound “pest” speakers. The bird-facing act is a **lid**. A short piezo click is optional and capped.

## Recommended build (if you can only ship one box)

One PETG **catchment-mouth bin**. Firmware can run **Approach B** (lid + bird-at-rim) as the Pass path. Approach A’s VR overlay registers to that same object. Two trays (dirty / “creek”) keep the stormwater story readable at three metres.
