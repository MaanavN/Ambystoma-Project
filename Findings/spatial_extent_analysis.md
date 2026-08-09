# CORRECTED Spatial Extent Analysis — Ohio + Bordering States

**Date:** 2026-08-06
**Correction:** Previous analysis used bounding-box queries which over-counted by ~2× (rectangular boxes captured observations from neighboring states). Correct counts use iNaturalist `place_id` filtering (state-level admin boundaries), matching the approach in `data_prep.ipynb`.

## Corrected Per-State Research-Grade Ambystoma Record Counts (place_id + captive=false)

| State | A. maculatum | A. jeffersonianum | A. tigrinum | A. opacum | A. texanum | A. barbouri | A. laterale | A. unisexual | Genus Total |
|-------|-------------|-------------------|-------------|-----------|------------|-------------|-------------|-------------|-------------|
| OH | 2,161 | 539 | 112 | 225 | 338 | 163 | 28 | 1,451 | 3,844 |
| IN | 790 | 132 | 202 | 930 | 315 | 182 | 146 | 1,000 | 2,723 |
| KY | 507 | 107 | 144 | 213 | 105 | 338 | 0 | 694 | 1,462 |
| MI | 597 | 0 | 143 | 0 | 8 | 0 | 2,239 | 2,441 | 3,042 |
| PA | 1,958 | 180 | 0 | 79 | 0 | 0 | 17 | 198 | 2,235 |
| WV | 428 | 15 | 0 | 45 | 2 | 0 | 0 | 17 | 490 |
| **Total** | **6,441** | **973** | **601** | **1,492** | **768** | **683** | **2,430** | **5,801** | **13,796** |

## Comparison: Bounding Box vs Place ID

| State | Bounding Box (wrong) | Place ID (correct) | Over-count |
|-------|-----------------------|--------------------|------------|
| OH | 4,160 | 3,844 | +316 |
| IN | 4,125 | 2,723 | +1,402 |
| KY | 5,183 | 1,462 | +3,721 |
| MI | 5,614 | 3,042 | +2,572 |
| PA | 4,908 | 2,235 | +2,673 |
| WV | 3,591 | 490 | +3,101 |
| **Total** | **27,584** | **13,796** | **+13,788** |

The bounding-box approach doubled the apparent record count by capturing observations from neighboring states that fell within the rectangular bounding boxes.

## Revised Assessment with Correct Counts

### What the 6-state extent buys us (corrected)
- **3.6× more data** than Ohio alone (13,796 vs 3,844 genus-level RG records)
- **A. laterale: 28 → 2,430** (87× increase, driven by Michigan's 2,239 records). Still the critical win — Ohio's endangered species goes from unmodelable to well-sampled.
- **A. tigrinum: 112 → 601** (5.4× increase). Goes from borderline to adequately sampled.
- **A. barbouri: 163 → 683** (4.2× increase, primarily from KY at 338). Streamside salamander now has solid coverage.
- **A. texanum: 338 → 768** (2.3× increase). Minor improvement.
- **A. opacum: 225 → 1,492** (6.6× increase, primarily from IN at 930). Good improvement.

### Species-level modelability with corrected counts

| Species | Corrected Total | Modelable? | Notes |
|---------|----------------|------------|-------|
| A. maculatum | 6,441 | ✅ Excellent | Plenty for full Maxent feature classes |
| A. unisexual | 5,801 | ✅ Excellent | Model as own entity |
| A. laterale | 2,430 | ✅ Good | Now viable thanks to Michigan data |
| A. opacum | 1,492 | ✅ Good | Strong in IN, adequate elsewhere |
| A. jeffersonianum | 973 | ✅ Adequate | But MI=0 is suspicious (ID issue) |
| A. tigrinum | 601 | ✅ Adequate | Distributed across OH, IN, MI, KY |
| A. texanum | 768 | ✅ Adequate | Absent from PA, marginal in MI/WV |
| A. barbouri | 683 | ✅ Adequate | Strong in KY, adequate in OH/IN |

**All 8 taxa are now modelable** with the 6-state extent. This was not the case with Ohio-only data (A. laterale at 28 records was unmodelable).
