# Spatial Extent Analysis — Ohio + Bordering States

**Date:** 2026-08-06

## Per-State Research-Grade Ambystoma Record Counts

| State | A. maculatum | A. jeffersonianum | A. tigrinum | A. opacum | A. texanum | A. barbouri | A. laterale | A. unisexual | Genus Total |
|-------|-------------|-------------------|-------------|-----------|------------|-------------|-------------|-------------|-------------|
| Ohio | 2,235 | 575 | 113 | 230 | 406 | 215 | 48 | 1,686 | 4,160 |
| Indiana | 1,099 | 160 | 315 | 967 | 367 | 290 | 686 | 2,056 | 4,125 |
| Kentucky | 1,750 | 283 | 244 | 1,524 | 462 | 556 | 0 | 1,555 | 5,183 |
| Michigan | 918 | 0 | 613 | 1 | 45 | 0 | 3,866 | 4,685 | 5,614 |
| Pennsylvania | 3,706 | 313 | 42 | 808 | 0 | 0 | 28 | 391 | 4,908 |
| West Virginia | 2,421 | 343 | 44 | 748 | 19 | 3 | 2 | 415 | 3,591 |
| **Total (sum)** | **12,129** | **1,674** | **1,371** | **4,278** | **1,299** | **1,064** | **4,630** | **10,788** | **27,581** |

## Key Observations

### What the 6-state extent buys us
- **6.6× more data** than Ohio alone (27,581 vs 4,160 genus-level RG records)
- **A. laterale jumps from 48 to 4,630** — driven almost entirely by Michigan (3,866). This is the critical improvement: the endangered species goes from unmodelable to well-sampled.
- **A. barbouri goes from 215 to 1,064** — now has solid coverage across OH, KY, IN.
- **A. unisexual goes from 1,686 to 10,788** — the hybrid complex is well-represented across the region.

### Problems this extent introduces

1. **Michigan's unisexual explosion:** Michigan has 4,685 unisexual records and 3,866 A. laterale — this is almost certainly because the Michigan DNR and iNaturalist community have a strong *Ambystoma* identification culture, and the A. laterale × A. jeffersonianum hybrid complex is ecologically dominant in the Great Lakes region. The "A. jeffersonianum = 0" for Michigan is suspicious — it likely means Michigan observers identify hybrids as "unisexual" rather than "jeffersonianum," which is actually more correct. But it means the A. jeffersonianum model would lose Michigan records that are ecologically relevant.

2. **A. texanum is absent from PA (0 records):** This species doesn't range into Pennsylvania. Including PA in the training extent for A. texanum would add environmental noise from areas where the species genuinely doesn't occur — the model would learn "PA conditions = absence" which is correct biologically but could bias predictions if PA's environmental range overlaps with where A. texanum DOES occur in OH/IN.

3. **A. barbouri is absent from MI and PA (0 records):** Same issue — the streamside salamander has a limited range. Training on states where it doesn't occur adds background noise.

4. **A. opacum is nearly absent from MI (1 record):** Marbled salamanders are at the northern edge of their range in Michigan. One record is likely a vagrant or misidentification. Including MI for A. opacum is marginal.

5. **Ecological gradient:** The 6-state extent spans from Kentucky (Appalachian/foothill ecology, 36.5°N) to Michigan's Upper Peninsula (boreal transition, 48°N). Salamander habitat relationships with temperature, forest type, and hydrology likely differ across this gradient. A model trained on Michigan's boreal transition zone forests might predict suitability in Ohio's glaciated till plains for the wrong reasons.

### The species-specific extent question
The per-state data reveals that **the optimal spatial extent differs by species**:

| Species | States where present (>50 records) | Suggested training extent |
|---------|-------------------------------------|--------------------------|
| A. maculatum | OH, IN, KY, MI, PA, WV | All 6 states |
| A. jeffersonianum | OH, IN, KY, PA, WV | 5 states (exclude MI — weird ID pattern) |
| A. tigrinum | OH, IN, KY, MI | 4 states (PA, WV marginal, but include for continuity) |
| A. opacum | OH, IN, KY, PA, WV | 5 states (exclude MI — 1 record) |
| A. texanum | OH, IN, KY | 3 states (absent from PA, absent from MI) |
| A. barbouri | OH, IN, KY | 3 states (absent from MI, PA, nearly absent from WV) |
| A. laterale | OH, IN, MI | 3 states (MI dominant, marginal elsewhere) |
| A. unisexual | OH, IN, KY, MI, WV, PA | All 6 states |

## The Decision

Two approaches:

**Option A: Fixed 6-state extent for all species.**
- Simpler pipeline, one background extent, one set of covariate rasters.
- Downside: some species get meaningless training data from states where they're absent. The model learns absences that are range edges, not habitat unsuitability.
- This is the standard practice in most SDM papers — they define a study area and use it for all species.

**Option B: Species-specific extents based on range.**
- Each species trained only in states where it has meaningful presence.
- More biologically defensible — no spurious absences from outside the range.
- Downside: more complex pipeline, different covariate rasters per extent, harder to compare across species.

**Recommendation:** Option A (fixed 6-state extent) for the primary analysis, with the caveat that we clip predictions to Ohio only. The 6-state extent is small enough that ecological gradients are manageable, and the benefit of A. laterale going from 48 to 4,630 records outweighs the noise from species' range edges. The key methodological choice is that **background points should be sampled only from within the 6-state extent** — not from a broader region — so the model compares presence against the right environmental baseline.

## Covariate implications
- A 6-state extent at NLCD 30m resolution is large (roughly 500km x 800km) but manageable computationally.
- WorldClim bioclim variables at 30 arc-seconds (~1km) will be coarse relative to NLCD but standard for climate.
- The covariate raster stack would cover a shared extent, simplifying the pipeline.
