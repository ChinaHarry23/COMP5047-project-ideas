# Architecture maps — Catchment bin for plastic-ingesting seabirds

Attach **`Architecture_A.svg`** and **`Architecture_B.svg`** as proposal figures. This page is the caption.

Open [`Architecture_map.html`](Architecture_map.html) in a browser if SVG preview is awkward.

## One-line (both)

World (bin, rain, bird-at-rim, high-risk litter) → sensors → ESP32-S3 fusion → lid / weir / flag. On-device care. **No screen as the product.** VR, if used, is a second station.

## Which map is which

| Figure | Approach | Care in the enclosure | Inquiry on the headset |
|---|---|---|---|
| A | Immersive awareness | Weir + two trays hold first-flush litter | Gut / ocean volume; 20.4% curve; gaze does not move servos |
| B | Bin guard | Lid closes when high-risk debris + bird-class body | Optional same overlay; still cannot fire the lid |

**Recommended:** print one PETG bin; run B’s FSM as Pass; hang A’s overlay on it after Week 9.

## Trust boundary

Nothing in the habitat *needs* a network. Image / BoM APIs are Credit. If they die, Approach A still sorts on turbidity; Approach B fail-closes the lid to a bird-class body. BLE is a maintainer dump, not a UI.

## Files

| File | What it is |
|---|---|
| `Architecture_A.svg` / `Architecture_B.svg` | Figures for Word / PDF |
| `Approach_A_VR.md` | Full Approach 1 design + week plan |
| `Approach_B_bin_guard.md` | Full Approach 2 design + week plan |
| `Hardware_stack.md` | Shared pins + two BOM deltas |
| `Software_stack.md` | Shared layers + both FSMs |
| `Hardware_BOM.md` | Order list |
