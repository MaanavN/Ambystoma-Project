# iNaturalist Data Exploration — Ambystoma in Ohio

**Date:** 2026-08-06
**Data source:** iNaturalist API v1 (https://api.inaturalist.org/v1)

---

## Summary

4,160 research-grade *Ambystoma* observations in Ohio as of Aug 2026. 8 identifiable taxa + a small number of genus-level-only records. Data is strongly seasonal (March peak), spatially biased, and growing rapidly year-over-year.

---

## Ohio Research-Grade Record Counts by Species

| Species | Common Name | Ohio RG | % of Total | Eastern US RG | Global RG |
|---------|-------------|---------|------------|---------------|-----------|
| *A. maculatum* | Spotted Salamander | 2,235 | 53.7% | 36,879 | 40,846 |
| *A. jeffersonianum* | Jefferson Salamander | 575 | 13.8% | 2,172 | 2,203 |
| *A. texanum* | Small-mouthed Salamander | 406 | 9.8% | 2,973 | 3,016 |
| *A. unisexual* (complex) | Unisexual Mole Salamander | 329 | 7.9% | — | 24,090 |
| *A. opacum* | Marbled Salamander | 230 | 5.5% | 11,445 | 11,788 |
| *A. barbouri* | Streamside Salamander | 215 | 5.2% | 996 | 1,126 |
| *A. tigrinum* | Eastern Tiger Salamander | 113 | 2.7% | 2,417 | 2,731 |
| *A. laterale* | Blue-spotted Salamander | 48 | 1.2% | 12,040 | 13,358 |
| *Ambystoma* (genus-level ID) | — | 9 | 0.2% | — | — |
| **Total (genus)** | | **4,160** | | **74,211** | **102,945** |

### Notes on the unisexual complex
- iNaturalist has a dedicated taxon for the unisexual *Ambystoma* (taxon ID 1457707, rank "complex").
- 329 Ohio records are identified as *A. unisexual* at research grade — these are the all-female hybrid lineages that cannot be assigned to parent species without genetic testing.
- These records are biologically real but complicate species-level modeling: they represent *A. laterale × A. jeffersonianum* hybrids primarily.
- The Eastern US count is unknown for this taxon (not retrieved separately).

---

## Seasonal Distribution (Ohio, Research-Grade)

```
Jan  ( 1):    21  ##
Feb  ( 2):   497  #################################################
Mar  ( 3):  2125  ####################################################################################################################################################################################################################
Apr  ( 4):   527  ####################################################
May  ( 5):   242  ########################
Jun  ( 6):   126  ############
Jul  ( 7):    93  #########
Aug  ( 8):    89  ########
Sep  ( 9):   176  #################
Oct  (10):   178  #################
Nov  (11):    65  ######
Dec  (12):    21  ##
```

**Interpretation:**
- **March dominates** (51.1% of all records) — this is the spring breeding migration when salamanders move to vernal pools and are encountered by observers.
- Secondary peak in Sep-Oct (marbled salamander fall breeding migration).
- Very few records in summer (Dec-Feb) when salamanders are underground.
- **Modeling implication:** The massive March peak reflects observation effort, NOT salamander distribution. The salamanders are present year-round; they're just visible in March. This is a form of temporal sampling bias.

---

## Positional Accuracy

| Accuracy Range | Count | Cumulative | % of Records with GPS |
|----------------|-------|-----------|----------------------|
| 0–10 m | 1,085 | 1,085 | 34.8% |
| 11–50 m | 661 | 1,746 | 21.2% |
| 51–100 m | 230 | 1,976 | 7.4% |
| 101–500 m | 643 | 2,619 | 20.6% |
| 501–1000 m | 107 | 2,726 | 3.4% |
| 1001–5000 m | 257 | 2,983 | 8.2% |
| 5001–100000 m | 122 | 3,105 | 3.9% |
| >100000 m | 12 | 3,117 | 0.4% |
| No GPS accuracy | — | — | 1,043 records |

**Interpretation:**
- ~63% of records with accuracy data have ≤100 m accuracy — good for species distribution modeling at 30 m (NLCD) to 1 km (WorldClim) resolutions.
- ~12% have accuracy >1 km — these are roughly locatable but may be at the county level or obscured for conservation purposes.
- 1,043 records (25%) have no positional accuracy metadata at all.
- **Recommendation:** Filter to ≤1000 m accuracy for fine-scale modeling; use all records for coarser-scale models (1 km+).

---

## Yearly Distribution

```
2007:    11
2008:    14
2009:     7
2010:     6
2011:    15
2012:    55     ← iNaturalist adoption begins
2013:    39
2014:    40
2015:    56
2016:    76
2017:    70
2018:   251     ← rapid growth
2019:   259
2020:   254     (COVID year — no dip, suggests outdoor observation remained strong)
2021:   380
2022:   426
2023:   366
2024:   622     ← recent surge
2025:   553
2026:   648  (partial year — already surpassing 2025)
```

**Interpretation:**
- iNaturalist data for Ohio *Ambystoma* really takes off in 2018 (likely due to app adoption + smartphone GPS improvement).
- 2024–2026 show strong acceleration — 600+ records per year.
- Earlier records (pre-2012) are sparse — likely museum specimens or early adopters.
- **Modeling implication:** More recent data is better quality (GPS accuracy, photo documentation). Consider weighting recent records more heavily, or at least be aware that older records contribute disproportionately to spatial coverage gaps.

---

## Target-Group Background Data

For sampling bias correction (target-group background approach):

| Target Group | Ohio RG Count |
|-------------|---------------|
| All *Ambystoma* (genus) | 4,160 |
| All salamanders (order Caudata) | 22,244 |
| All amphibians (class Amphibia) | 67,199 |

**Recommendation:** Use all salamander (Caudata, n≈22K) observations as the target-group background. This reflects where salamander observers go, which is the most relevant sampling bias for *Ambystoma* — the people who report salamanders are the same people who would report *Ambystoma*. Using all amphibians (n≈67K) would include frog/toad observers who may not search the same habitats.

---

## Data Quality Assessment

### Strengths
1. **Adequate sample sizes** for the common species: *A. maculatum* (2,235) and *A. jeffersonianum* (575) have enough records for species-level Maxent with proper tuning.
2. **Moderate samples** for *A. texanum* (406), *A. unisexual* (329), *A. opacum* (230), *A. barbouri* (215) — workable for Maxent with conservative feature classes.
3. **Small but potentially viable** for *A. tigrinum* (113) — borderline; may need to aggregate.
4. **Too few** for *A. laterale* (48) — endangered in Ohio, likely needs genus-level modeling or broader spatial extent.

### Weaknesses
1. **Severe spatial sampling bias**: Observations cluster near cities (Cleveland, Columbus, Cincinnati), parks, and roads. Rural areas are underrepresented.
2. **Strong seasonal bias**: 51% of records from March alone — observer effort concentrated on breeding migration, not true habitat occupancy.
3. **Positional accuracy varies widely**: 25% of records have no GPS accuracy metadata; 12% have accuracy >1 km. Fine-scale covariate extraction (e.g., NLCD 30 m) will be noisy for these records.
4. **Taxonomic confusion**: The unisexual complex (8% of records) complicates species-level modeling. Records of *A. jeffersonianum* and *A. laterale* may include hybrid individuals that can't be distinguished without genetics.
5. **Temporal extent limited**: Pre-2018 data is sparse. The rapid growth in recent years means geographic coverage is still expanding.

---

## Modeling Implications

### Species-level vs genus-level
- **Species-level is feasible** for the top 6 taxa (≥200 records each): *A. maculatum*, *A. jeffersonianum*, *A. texanum*, *A. unisexual*, *A. opacum*, *A. barbouri*.
- **A. tigrinum** (113 records) — borderline. Can be modeled alone with very conservative settings, or aggregated.
- **A. laterale** (48 records) — too few. Model genus-level in regions where A. laterale is expected, or combine with A. jeffersonianum (since they hybridize and share similar habitat).
- **A. barbouri** — model separately regardless. Its streamside niche is ecologically distinct from the vernal-pool species.

### Spatial extent
- Training on eastern US (74K RG records) vs Ohio only (4.2K) — broader training captures more environmental variation and species range edges, but may include ecological regions where habitat relationships differ.
- **Recommendation:** Train on a mid-scale extent (e.g., Great Lakes / Midwest region: OH, IN, IL, MI, KY, PA, WV) — enough records to capture the species' regional ecology, small enough that habitat relationships are relatively consistent.

### Spatial thinning
- Apply spatial thinning at ~1 km minimum distance to reduce observer-hotspot clustering.
- This will reduce the effective sample size (rough estimate: from 2,235 → ~500–800 unique cells for *A. maculatum*), still adequate for Maxent.

### Background point strategy
- Use target-group background: sample background from locations where all salamander observations (Caudata, n≈22K) have been reported.
- This corrects for the sampling bias that affects *Ambystoma* observations.
