# Evidence pack — Wildlife-First Adaptive Path Light

Use this for the Week 4 proposal (short cites) and the final report (full argument). Prefer **Sydney / Australian empirical papers + DCCEEW** over UK reviews. UK papers are background, not the proof that *these* species need darkness.

## How to argue (one paragraph you can reuse)

Artificial light at night is increasing faster than almost any other pollutant (Kyba et al. 2023). In Greater Sydney, light at the urban-forest edge reduces activity of clutter-adapted microbats and keeps them inside remnants, while faster open-space species still fly the lit edge (Haddock et al. 2019a; Threlfall et al. 2013). Cool-white and even “warm-filtered” LEDs pull flying insects out of vegetation; bats do not correspondingly increase feeding, so the light is a prey vacuum, not a reliable snack bar (Robert et al. 2025; Owens & Lewis 2018). Australian national guidance is therefore: keep connecting corridors dark; if light is required, use low-intensity amber/red, point it down, shield it, and use sensors/timers so it is on only when a person is there (DCCEEW 2023). Our bollard is that rule as a device: default off, 2200 K / PC-amber, 180° bush shield, presence-triggered, bat-band extra shielding.

Do **not** claim Gould’s wattled bat or white-striped free-tailed bat “need darkness.” Sydney data say they are light-tolerant at forest edges (Haddock et al. 2019a). Claim instead: they still do not gain a feeding benefit under LEDs (Robert et al. 2025), and lighting the same edge still harms the light-sensitive species that share it.

---

## Claim → source → design move

| Claim | Best source | What we build |
|---|---|---|
| ALAN is a fast-growing pollutant | Kyba et al. 2023, *Science* (~9.6%/yr skyglow); Hölker et al. 2010 | Challenge statement |
| Sydney clutter-bats avoid lit edges / urban matrix | Threlfall et al. 2013 (radio-tracked *N. gouldi* in Cumberland State Forest); Haddock et al. 2019a (31 Sydney forest edges) | Primary stakeholder; default-off; shield the bush |
| Light-sensitive Sydney assemblage is broader than one species | Haddock et al. 2019a: *Nyctophilus* spp., *C. morio*, *Miniopterus australis*, *Vespadelus vulturnus* down at lit edges; *V. vulturnus* emergence delayed | Name *N. gouldi* as exemplar of a guild |
| *C. gouldii* and *A. australis* use lit and dark edges similarly | Haddock et al. 2019a | They are **acoustic proxies / co-users**, not the darkness-need |
| *C. gouldii* is physiologically light-exploiting | Dimovski, Robert & Griffiths 2023, *RSOS* (no melatonin change under red/amber/warm/cool LEDs) | Do not hang ethics on this species |
| White/filtered LEDs vacuum insects; bats do not increase feeding | Robert et al. 2025, *Ecosphere* (Vic remnant bushland; CW λP 457 nm, FW 586 nm, amber 601 nm) | Amber/2200 K; default-off protects prey base |
| Amber reduces disruption for at least one light-sensitive bat | Robert et al. 2025; Spoelstra et al. 2017 (EU: light-shy bats avoid white/green, not red) | 2200 K / PC-amber LED |
| LED streetlight upgrades in Sydney still hurt clutter/edge bats and cut *C. gouldii* activity | Haddock et al. 2019b, *Austral Ecology* (MV → low-UV LED) | Unlit remnants matter; “LEDs are green” is not a wildlife argument |
| Melbourne coastal: activity and richness higher in dark than under streetlight | Linley 2016, *Australian Mammalogy* | Same pattern outside Sydney |
| White light + UV/blue attracts insects; many die | Owens & Lewis 2018; Owens et al. 2020 (~1/3 of insects at lamps die) | Tertiary stakeholder (moths) |
| Official AU recipe: dark corridors; if needed, low-intensity red/amber, downward, shielded, ≥50 m from habitat, sensors/timers | DCCEEW 2023 *National Light Pollution Guidelines*; DCCEEW bats factsheet | Every actuation maps to a named guideline |
| When light is required for safety, use motion-triggered / dynamic schemes | Voigt et al. 2018, EUROBATS Publication Series 8 | Presence sensor + fade-up/fade-down |
| Fast vs slow bats respond differently; dimming/direction/part-night are the levers | Stone et al. 2015; Rowse et al. 2016 | Blade + dim-with-floor + short duration |
| *C. gouldii* search calls ~25–34 kHz; *A. australis* ~10–15 kHz (audible); *Nyctophilus* steep FM, faint, ~40–80 kHz | Pennay, Law & Reinhold, *Bat calls of NSW* | Dual-band analog front-end. **20–50 kHz alone misses white-striped search calls and most *Nyctophilus*.** |
| Infer needs from published consensus; do not claim to know animal intent | Mancini 2011, 2016 (ACI); Forlano et al. 2022 (smart lighting + non-humans) | “Operationalise darkness as default” |

---

## Core Australian / Sydney papers (cite these first)

1. **Threlfall, C.G., Law, B. & Banks, P.B. (2013).** The urban matrix and artificial light restricts the nightly ranging behaviour of Gould’s long-eared bat (*Nyctophilus gouldi*). *Austral Ecology*, 38(8), 921–930. https://doi.org/10.1111/aec.12034  
   Radio-tracked 19 *N. gouldi* in **Cumberland State Forest, north-west Sydney**. Nightly range ≤80 ha; moved on average <300 m from roosts; almost obligate bushland use. Lit open spaces inside the remnant had **lower *Nyctophilus* activity** and higher activity of urban-tolerant species. Light **spills into** preferred habitat.

2. **Haddock, J.K., Threlfall, C.G., Law, B. & Hochuli, D.F. (2019a).** Light pollution at the urban forest edge negatively impacts insectivorous bats. *Biological Conservation*, 236, 17–28. https://doi.org/10.1016/j.biocon.2018.11.005  
   **31 forest edges across Greater Sydney**, half mercury-vapour streetlights, half dark, vs interiors. 9965 passes, 16 taxa. Interiors always busier. Light-sensitive: *C. morio*, *M. australis*, *V. vulturnus*, *Nyctophilus* spp. Light-tolerant: *C. gouldii*, *Ozimops ridei*, ***Austronomus australis***, *Saccolaimus flaviventris*, *M. orianae oceanensis*. Larger moths drawn to lit edges in low numbers. Treat ALAN as an **anthropogenic edge effect**.

3. **Haddock, J.K., Threlfall, C.G., Law, B. & Hochuli, D.F. (2019b).** Responses of insectivorous bats and nocturnal insects to local changes in street light technology. *Austral Ecology*. https://doi.org/10.1111/aec.12772  
   Sydney **mercury-vapour → LED**. Clutter/edge species more active in unlit bush than any lit site, and **dropped after LED install**. Fast-flying ***C. gouldii* also decreased** after the LED switch (less UV/insect draw). Unlit remnants are the refuge. “Preventing light penetration into unlit bushland patches and corridors remains essential.”

4. **Robert, K.A., Dimovski, A.M., Contos, P., Khwaja, N. & Griffiths, S.R. (2025).** Divergent responses of insectivorous bats and flying insects to experimental LED illumination of different spectra. *Ecosphere*, 16(5), e70291. https://doi.org/10.1002/ecs2.70291  
   Experiment in **previously unlit remnant bushland** (Victoria): cool white, filtered white, amber, dark control. **CW and FW significantly increased flying insects** (Diptera, Coleoptera, Hymenoptera). Total bat activity did not follow. **No increase in feeding buzzes** under lights. Amber mitigated disruption for at least one light-sensitive species. Species that persist are **light-tolerant, not light-exploitive feeders**.

5. **Dimovski, A.M., Robert, K.A. & Griffiths, S.R. (2023).** A light-exploiting insectivorous bat shows no melatonin disruption under lights with different spectra. *Royal Society Open Science*, 10, 221436. https://doi.org/10.1098/rsos.221436  
   *C. gouldii* urinary melatonin unchanged under red, amber, filtered warm white, and cool white LEDs (short-term). Confirms the species as a **poor primary stakeholder** for a “darkness-need” story. Use it as the loud acoustic proxy.

6. **Linley, G.D. (2016).** The impact of artificial lighting on bats along native coastal vegetation. *Australian Mammalogy*. https://doi.org/10.1071/AM15047  
   Melbourne south-east: **activity and species richness higher at unlit** native vegetation than at streetlit treatments. Activity at lit sites declined through the night; unlit sites stayed high until ~2 h before sunrise.

7. **DCCEEW (2023).** *National Light Pollution Guidelines for Wildlife* (bats appendix added 2023). https://www.dcceew.gov.au/environment/biodiversity/publications/national-light-pollution-guidelines-wildlife  
   Companion: https://www.dcceew.gov.au/campaign/light-pollution/bats  
   **Do not light** roosts, feeding sites, water, connecting corridors. If lights are needed: **low-intensity red or amber**, **≥50 m** from bat habitat, **downward**, **shielded**, vegetation as a screen. White is harmful to a wide range of bats. Amber/red less likely to vacuum insects.

---

## International reviews (use as background, not as Sydney proof)

8. **Stone, E.L., Harris, S. & Jones, G. (2015).** Impacts of artificial lighting on bats: a review of challenges and solutions. *Mammalian Biology*, 80, 213–219. https://doi.org/10.1016/j.mambio.2015.02.004

9. **Rowse, E.G., Lewanzik, D., Stone, E.L., Harris, S. & Jones, G. (2016).** Dark matters: the effects of artificial lighting on bats. In Voigt, C.C. & Kingston, T. (eds) *Bats in the Anthropocene*, Springer, 187–213. https://doi.org/10.1007/978-3-319-25220-9_7  
   Fast aerial hawkers at lamps; slow clutter bats light-averse. Mitigation: part-night, dimming, directed light, **motion-sensitive lighting**.

10. **Stone, E.L., Jones, G. & Harris, S. (2009).** Street lighting disturbs commuting bats. *Current Biology*, 19, 1123–1127. (Classic experimental commute-barrier paper.)

11. **Stone, E.L., Jones, G. & Harris, S. (2012).** Conserving energy at a cost to biodiversity? Impacts of LED lighting on bats. *Global Change Biology*, 18, 2458–2465.

12. **Spoelstra, K. et al. (2017).** Response of bats to light with different spectra: light-shy and agile bat presence is affected by white and green, but not red light. *Proceedings of the Royal Society B*, 284, 20170075. https://doi.org/10.1098/rspb.2017.0075

13. **Voigt, C.C. et al. (2018).** *Guidelines for consideration of bats in lighting projects.* EUROBATS Publication Series No. 8. UNEP/EUROBATS, Bonn.  
   Avoid light wherever possible. If safety requires it: **dynamic / motion-triggered** schemes.

14. **Kyba, C.C.M. et al. (2023).** Citizen scientists report global rapid reductions in the visibility of stars from 2011 to 2022. *Science*, 379, 265–268. https://doi.org/10.1126/science.abq7781  
   ~**9.6% per year** skyglow increase (Globe at Night). Stronger than satellite estimates because satellites miss blue LED.

15. **Hölker, F. et al. (2010).** Light pollution as a biodiversity threat. *Trends in Ecology & Evolution*, 25(12), 681–682.

16. **Owens, A.C.S. & Lewis, S.M. (2018).** The impact of artificial light at night on nocturnal insects: a review and synthesis. *Ecology and Evolution*, 8, 11337–11358. https://doi.org/10.1002/ece3.4557

17. **Owens, A.C.S. et al. (2020).** Light pollution is a driver of insect declines. *Biological Conservation*, 241, 108259.

---

## Acoustic / species ID (hardware justification)

18. **Pennay, M., Law, B. & Reinhold, L.** *Bat calls of New South Wales: region-based guide to the echolocation calls of microchiropteran bats.* NSW Department of Environment.  
    - *C. gouldii*: characteristic frequency **25–34 kHz**, alternating pulses.  
    - *A. australis*: **~10–15 kHz**, often audible; only NSW bat below 15 kHz.  
    - *Nyctophilus*: steep near-vertical FM, low intensity, easily missed.

**Hardware implication:** a 20–50 kHz-only detector is a *C. gouldii* detector, not a white-striped or long-eared detector. Use analog frequency-division (wideband) **or two gates**: ~10–15 kHz **and** ~25–35 kHz. Do not promise live *Nyctophilus* detection in the demo.

Playback files: NSW full-spectrum examples at https://www.nsw.chiroptera.se/ (e.g. *Austronomus*, *C. gouldii*).

Site species list (runtime not required): Atlas of Living Australia / iNaturalist for the chosen park.

---

## More-than-human / ACI (framework, not ecology)

19. **Mancini, C. (2011).** Animal-computer interaction: a manifesto. *interactions*, 18(4), 69–73.

20. **Mancini, C. (2016).** Towards an animal-centred ethics for Animal–Computer Interaction. *International Journal of Human-Computer Studies*, 98, 221–233.

21. **Forlano, L., Golembiewski, J. & Jenkins, T. (2022).** Would the trees dim the lights? Adopting the intentional stance for more-than-human participatory design. *PDC ’22*. https://doi.org/10.1145/3537797.3537799  
    Directly about **smart lighting** and non-human stakeholders.

22. **Heitlinger, S., Foth, M. & Clarke, R. (eds) (2024).** *Designing More-than-Human Smart Cities.* (Context for the unit’s “non-human participants” brief.)

---

## What not to over-claim

- Do not cite only Stone/Rowse as if they measured Sydney bats.
- Do not say *C. gouldii* “needs darkness.” Haddock 2019a and Dimovski 2023 cut that claim down.
- Do not say the prototype will increase local bat populations in one semester.
- Do not say 192 kHz on-device FFT is required; ecology does not demand species-ID in the bollard, only a conservative “something in the bat band → extra shield.”
- 2200 K still has some blue. DCCEEW prefers **amber/red**. If you cannot buy PC-amber, say 2200 K is a compromise on the way to amber, and cite Robert et al. 2025 (amber λP 601 nm helped).

## Open-access starting points

- Haddock 2019a manuscript: https://nespurban.edu.au/wp-content/uploads/2019/06/Bats_lightsattheedge_Haddock-et-al-2019-Biol-Cons.pdf  
- Haddock 2019b manuscript: https://nespurban.edu.au/wp-content/uploads/2019/06/MVtoLEDPaper_Haddock-et-al-2019-Aust-Ecol.pdf  
- Dimovski et al. 2023: https://doi.org/10.1098/rsos.221436 (open)  
- Robert et al. 2025: https://doi.org/10.1002/ecs2.70291 (open)  
- EUROBATS lighting guide: https://www.eurobats.org/sites/default/files/documents/publications/publication_series/WEB_EUROBATS_08_ENGL_NVK_19092018.pdf  
- DCCEEW bats: https://www.dcceew.gov.au/campaign/light-pollution/bats  
- NSW bat-call guide: https://www.environment.nsw.gov.au/sites/default/files/2024-09/bat-calls-of-nsw.pdf  
