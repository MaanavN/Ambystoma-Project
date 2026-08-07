# Literature Review — Ambystoma Species Distribution Modeling

**Compiled:** 2026-08-06
**Purpose:** Inform modeling approach for predicting *Ambystoma* salamander habitat suitability in Ohio using iNaturalist occurrence data + environmental covariates.

---

## 1. Foundational SDM Theory

### 1.1 Phillips, Anderson & Schapire (2006) — Maxent for SDM
**"Maximum entropy modeling of species geographic distributions"**
*Ecological Modelling* 190:231–259

- **In repo:** `Literature/Maximum entropy modeling of species geographic distributions.pdf`
- The foundational paper for using Maxent (maximum entropy) with presence-only data for species distribution modeling.
- **Core idea:** Estimate the probability distribution of a species across geography that is most spread out (maximum entropy) subject to constraints derived from environmental conditions at known occurrence points.
- Presence-only: no absence data needed. Uses "background" points (random samples of the landscape) instead.
- Tested on two Neotropical mammals (*Bradypus variegatus*, *Microryzomys minutus*) at continental scale. Maxent outperformed GARP (an older presence-only method) in AUC.
- Handles both continuous (climate, elevation) and categorical (vegetation type) features.
- Additive model: each variable contributes independently at each pixel.
- Regularization prevents overfitting — critical with small occurrence counts.
- AUC (area under ROC curve) is the standard evaluation metric.

### 1.2 Phillips & Dudík (2008) — Maxent Extensions & Evaluation
**"Modeling of species distributions with Maxent: new extensions and a comprehensive evaluation"**
*Ecography* 31:161–175

- Extended the 2006 work with empirical parameter tuning.
- Introduced automatic feature selection and regularization based on sample size.
- Evaluated Maxent against other presence-only methods across 226 species — Maxent performed best on average.
- Introduced "threshold features" and "hinge features" as additional feature transformations.
- Key finding: Maxent's default parameters are tuned based on sample size, but further tuning can improve performance, especially for small samples.

### 1.3 Elith & Leathwick (2009) — SDM Review
**"Species Distribution Models: Ecological Explanation and Prediction Across Space and Time"**
*Annual Review of Ecology, Evolution, and Systematics* 40:677–697

- Comprehensive review of SDM methods, their ecological assumptions, and applications.
- Key for understanding the theoretical foundations: SDMs estimate the environmental niche ( Hutchinson's fundamental niche) from occurrence data.
- Discusses the distinction between presence-only, presence-absence, and abundance models.
- Emphasizes that model choice should be driven by the data available and the question asked — not by convention.
- Identifies sampling bias, spatial autocorrelation, and model evaluation as key challenges.

### 1.4 Merow, Smith & Silander (2013) — Practical Maxent Guide
**"A practical guide to MaxEnt for modeling species' distributions: what it does, and why inputs and settings matter"**
*Ecography* 36:1058–1069

- The definitive "how to use Maxent properly" guide.
- Explains: background selection, feature types (linear, quadratic, product, threshold, hinge), regularization multiplier, model output interpretation, and evaluation.
- **Key recommendations:**
  - Background points should reflect the area accessible to the species (M in the BAM framework).
  - Feature classes should be chosen based on sample size: fewer features for small samples.
  - Regularization multiplier should be tuned (not left at default) — higher values = smoother, more general models.
  - AUC is useful but has limitations, especially with presence-only data (background is not true absence).
  - Sampling bias correction: can use target-group background or spatial filtering.
- **Critical for our project:** This is the guide Lautenbach would have read. Our Maxent implementation should follow these recommendations.

---

## 2. Maxent vs. Random Forest for SDM

### 2.1 Zhang et al. (2022) — Maxent vs RF Comparison
**"Comparison between optimized MaxEnt and random forest modeling in predicting potential distribution"**
*Science of the Total Environment* (Quasipaa boulengeri case study)

- Optimized both Maxent (tuned feature classes + regularization) and RF.
- RF had higher AUC ratio and better predictive performance than Maxent after optimization.
- Maxent requires optimization to perform well; default settings are often suboptimal.
- Both methods produced reasonable distribution maps.

### 2.4 Mi et al. (2027) — RF for Rare Species with Few Samples
**"Why choose Random Forest to predict rare species distribution with few samples in large undersampled areas?"**
*PeerJ* 5:e2849

- Compared RF, Maxent, CART, and TreeNet for three rare Asian crane species with few occurrence records.
- **RF consistently had the highest AUC** (>0.625), followed by Maxent (>0.558), then CART/TreeNet.
- RF robust to small sample sizes and handles interactions between variables automatically.
- Key for rare/threatened species surveys — exactly our use case.

### 2.3 Zurell et al. (2022) — Comprehensive SDM Algorithm Comparison
**"Comprehensively evaluating the performance of species distribution models across clades and resolutions"**
*Landscape Ecology* 37:1293–1314

- Compared 11 SDM algorithms across trees, birds, mammals, and insects.
- RF and BRT (boosted regression trees) outperformed in AUC (mean 0.938) across all clades.
- Decision trees, MaxLike, and Lasso underperformed (AUC 0.848).
- **No single algorithm was best for all species** — model choice depends on data characteristics.

### 2.4 Hage et al. (2026) — Flexible Methods for Small Samples
**"Flexible methods for species distribution modeling with small samples"**
*Ecography*

- Most recent and directly relevant comparison.
- Found that **no algorithm performed best in all situations**.
- Across all species, Maxent performed best on average but was outperformed by one or more alternatives depending on the species.
- With small samples, model choice matters more than with large samples.
- Recommendation: try multiple algorithms and compare.

---

## 3. Small Sample Size & Maxent Configuration

### 3.1 Morales, Fernández & Baca-González (2017) — Small Samples Systematic Review
**"MaxEnt's parameter configuration and small samples: are we paying attention to recommendations?"**
*PeerJ* 5:e3093

- **Critical paper for our project.** Reviewed 244 Maxent articles (2013–2015).
- Only 16% of studies evaluated feature classes, 6.9% evaluated regularization multiplier, and just 3.7% tuned both parameters.
- Using default Maxent parameters with small samples produces over-complex or over-simplistic models.
- Quantified that default parameters vs. tuned models produce **important differences in the total area identified as suitable and the specific locations identified.**
- **Direct relevance:** iNaturalist occurrence records for Ohio *Ambystoma* will almost certainly be "small N" (likely <100 research-grade records per species). We MUST tune Maxent's parameters rather than using defaults.
- Feature class recommendations by sample size (from Maxent software defaults):
  - <5 occurrence points: only linear features
  - 5–9: linear + quadratic + product
  - 10–14: + hinge
  - 15–79: + threshold
  - ≥80: all features (linear, quadratic, product, hinge, threshold)

### 3.2 Shcheglovitova & Anderson (2013)
- Showed that specific combinations of feature classes and regularization multipliers outperform defaults.
- Recommends careful tuning especially when sample sizes are <25.

### 3.3 Radosavljevic & Anderson (2014)
- Demonstrated that default regularization often overfits with small samples.
- Species-specific tuning increases robustness to sampling bias.

---

## 4. iNaturalist Data — Bias and Correction

### 4.1 Sampling Bias in iNaturalist Data

iNaturalist is a community-science (citizen science) platform where users report observations of plants and animals. The data is presence-only and subject to **severe spatial sampling bias:**

- Observations cluster near roads, urban areas, parks, and trails (accessibility bias).
- Observer density varies dramatically by region (more observations near cities, fewer in rural areas).
- Charismatic species get more reports than cryptic species (salamanders may be under-reported).
- Seasonal bias: most observations during spring/summer when people are outdoors and salamanders are migrating to breeding pools.

**Sources identified:**
- Geurts et al. (2023) — "Turning observations into biodiversity data: Broadscale spatial biases in community science" (*Ecosphere*). Demonstrates workflow to identify spatial sampling bias in iNaturalist data.
- Steen et al. (2021) — spatial thinning to reduce over-representation at observer hotspots.
- Callaghan et al. (2021) — "Observing the Observers" (*BioScience*). Quantifies how iNaturalist participants contribute data and implications for SDM.
- "Not all who wander are lost" (PLOS One, 2023) — trail bias in community science: higher richness on trails than off trails.

### 4.2 Bias Correction Methods

Four main approaches for handling sampling bias in presence-only SDMs:

1. **Spatial thinning (STSP):** Remove occurrence records that are too close together (e.g., minimum distance between points). Reduces clustering from observer hotspots. Simple but discards data.
   - Tool: `spThin` R package (Aiello-Lammens et al. 2015)

2. **Target-group background (TGOB):** Instead of random background points, sample background from areas where the "target group" (e.g., all amphibian observations, or all salamander observations) have been reported. This ensures the background reflects the same sampling bias as the occurrence data — the model learns environmental differences rather than sampling patterns.
   - Phillips 2008 recommends this approach.
   - Caveat: can overcorrect, replacing underestimation bias with overestimation bias in low-sampling areas (Wort & [performance tradeoffs study], *Ecography* 2016).

3. **Environmental filtering (E-Filter):** Remove occurrence records that are too similar in environmental space, not geographic space. Useful when environmental redundancy is the concern.

4. **Geographic filtering (G-Filter):** Remove records by selecting one record per grid cell at a given resolution.

**Recommendation for our project:** Start with spatial thinning + target-group background. The target group should be all Ohio amphibian (or all salamander) iNaturalist observations — this gives the best estimate of sampling effort relevant to *Ambystoma*.

---

## 5. Software & Tools

### 5.1 elapid — Python Maxent
- **Repository:** https://github.com/earth-chris/elapid
- **Docs:** https://earth-chris.github.io/elapid/
- **JOSS paper:**earth-chris (2023), *Journal of Open Source Software* 8(84):4930
- Python implementation of Maxent, built to match `maxnet` (the R version).
- Behaves like an sklearn estimator (`model.fit(X, y)`, `model.predict(X)`).
- Key configuration parameters:
  ```python
  model = elapid.MaxentModel(
      feature_types=['linear', 'hinge', 'product'],  # feature transformations
      tau=0.5,               # prevalence scaler
      clamp=True,            # clamp covariates to training range
      scorer='roc_auc',      # optimization metric
      beta_multiplier=1.5,   # regularization scaler (higher = smoother)
      beta_lqp=1.0,          # linear/quadratic/product regularization
      beta_hinge=1.0,        # hinge regularization
      beta_threshold=1.0,    # threshold regularization
      beta_categorical=1.0,  # categorical regularization
      n_hinge_features=10,
      n_threshold_features=10,
      use_lambdas='best',    # 'best' (least overfit) or 'last'
  )
  ```
- **Opinionated defaults** (differ from maxnet/Java Maxent):
  - `beta_multiplier=1.5` (vs 1.0 in maxnet) — more regularization by default
  - `use_lambdas='best'` (vs 'last') — selects least-overfit model
  - `feature_types=['linear', 'hinge', 'product']` — excludes quadratic, threshold by default to reduce complexity
- Can annotate point locations with raster covariate values (vector→raster bridge).
- Supports raster prediction (apply trained model to raster stack for suitability maps).

### 5.2 biomod2 — Ensemble SDM (R)
- **CRAN:** https://cran.r-project.org/web/packages/biomod2/
- Ensemble platform: runs up to 10 single SDM algorithms, combines them into an ensemble.
- Supported algorithms: GLM, GBM, RF, Maxent, GAM, ANN, CART, FDA, XGBoost, etc.
- Ensemble approaches (weighted mean, committee averaging) reduce model-specific uncertainty.
- R-only — would need R integration if we use it.

### 5.3 pyinaturalist — iNaturalist API Client (Python)
- **Docs:** https://pyinaturalist.readthedocs.io/
- **PyPI:** `pip install pyinaturalist`
- Python client for iNaturalist REST API (v1).
- Can query observations by taxon, geography, date, quality grade.
- Research-grade observations are automatically shared with GBIF.
- Key endpoints: `GET /observations` (search observations), `GET /observations/species_counts`

### 5.4 GBIF Occurrence Data
- iNaturalist research-grade observations are exported to GBIF.
- GBIF API: https://www.gbif.org/occurrence/download/
- GBIF iNaturalist dataset key: `50c9509d-22c7-4a22-a47d-...`
- Can download occurrence data as CSV with coordinates, basis of record, etc.
- Max 100,000 records per API download.

---

## 6. Environmental Covariate Data Sources

### 6.1 Climate — WorldClim
- 19 bioclimatic variables (BIO1–BIO19): temperature, precipitation, seasonality.
- Resolutions: 30 arc-seconds (~1 km), 2.5 arc-minutes, 5 arc-minutes, 10 arc-minutes.
- Standard for SDM; used in Phillips et al. (2006) and most SDM studies.
- Website: https://www.worldclim.org/
- Python access: `geodata` R package or direct download.

### 6.2 Land Cover — NLCD (National Land Cover Database)
- US nationwide land cover at 30 m resolution.
- Categories: forest (deciduous, evergreen, mixed), wetland (woody/herbaceous), developed (open/low/medium/high intensity), cultivated crops, pasture, barren, open water, etc.
- Updated roughly every 3 years (2001, 2004, 2006, 2008, 2011, 2013, 2016, 2019, 2021).
- Website: https://www.mrlc.gov/
- **Highly relevant for salamanders:** forest cover, wetland presence, and developed land are key habitat determinants.

### 6.3 Soil — SSURGO / gSSURGO
- USDA NRCS Soil Survey Geographic Database.
- Most detailed soil data in the US — typically 1:12,000 to 1:24,000 scale.
- gSSURGO = gridded version, raster format, ready for GIS stacking with NLCD, etc.
- **Critical for salamanders:** soil type, drainage class, hydric soil designation (wetland indicator), and soil texture all directly matter for burrowing salamanders.

### 6.4 Elevation — USGS National Elevation Dataset (NED)
- 1/3 arc-second (~10 m) and 1 arc-second (~30 m) resolution.
- Can derive slope, aspect, flow accumulation, topographic wetness index.
- Salamanders are sensitive to topography — slope, aspect, and flow patterns affect moisture and pool formation.

### 6.5 Other potential layers
- **NHD (National Hydrography Dataset):** streams, waterbodies, wetlands. Proximity to water is key for amphibians.
- **EPA EnviroAtlas:** ecosystem services data including wetland connectivity.
- **Cropland Data Layer (CDL):** USDA NASS — agricultural land use at 30 m. Relevant for distinguishing habitat from cropland.
- **TNC (The Nature Conservancy) ecoregions:** for defining the study area boundary in environmentally meaningful units.

---

## 7. Ohio *Ambystoma* Species

Based on the Ohio ODNR Amphibians Field Guide, Ohio Herp Atlas (OHPARC), and Ohio's Endangered Species list:

| Species | Common Name | Ohio Status | Notes |
|---------|-------------|-------------|-------|
| *Ambystoma maculatum* | Spotted Salamander | Common | Most common Ambystoma in Ohio; breeds in vernal pools in forests |
| *Ambystoma jeffersonianum* | Jefferson Salamander | Common (state) | Early spring breeder; found in woodland pools |
| *Ambystoma tigrinum* | Eastern Tiger Salamander | Common | Ohio's largest terrestrial salamander; proficient digger |
| *Ambystoma opacum* | Marbled Salamander | Common | Fall breeder; uses seasonal ponds |
| *Ambystoma texanum* | Small-mouthed Salamander | Common | Spring migrator; found in various habitats |
| *Ambystoma barbouri* | Streamside Salamander | — | Uses headwater streams rather than vernal pools |
| *Ambystoma laterale* | Blue-spotted Salamander | **Endangered (OH)** | Rare in Ohio; resembles Jefferson; hybridization complicates ID |
| Unisexual *Ambystoma* | (complex, not a species) | — | All-female hybrid lineages (A. laterale × A. jeffersonianum complex); cannot be identified without genetic testing |

**Key ecological notes:**
- All *Ambystoma* are "mole salamanders" — adults are fossorial (underground) outside breeding season.
- Most breed in fishless vernal pools (temporary woodland ponds); *A. barbouri* is the exception (streams).
- Use abandoned small mammal/burrows or crayfish burrows; some dig their own.
- Habitat requirements: forest with nearby seasonal wetlands, moist soil, leaf litter.
- Spring migration to breeding pools triggered by rain and temperature.

**Modeling implications:**
- Forest cover, wetland proximity, soil moisture/drainage, and vernal pool availability are likely the most predictive covariates.
- Climate variables (temperature, precipitation) may matter for regional distribution but less so at the Ohio scale.
- *A. barbouri* has a distinct niche (streamside) and should be modeled separately if included.
- The unisexual complex complicates species-level modeling — these records may need to be merged with parent species or excluded.

---

## 8. Directly Relevant Prior Work

### 8.1 Jefferson Salamander SDM (Illinois)
**"Using species distribution and occupancy modeling to guide survey efforts for a rare salamander"**
*Ambystoma jeffersonianum* in Illinois — [ScienceDirect link](https://www.sciencedirect.com/science/article/abs/pii/S1617138112001148)

- Modeled a threatened salamander (*A. jeffersonianum*) in Illinois using SDM + occupancy modeling.
- Used fine-scale environmental covariates.
- **Directly relevant precedent:** same genus, similar conservation context, using SDM to guide surveys.
- Found that fine-scale distribution models are useful for prioritizing survey locations.

### 8.2 Salamandra atra aurorae SDM (land cover resolution)
**"Species distribution models for the conservation of a micro-endemic salamander"**
*Springer* (2025)

- Assessed efficacy of regional vs. continental land cover datasets for predicting habitat suitability for a micro-endemic salamander.
- Land cover resolution matters — finer-scale data improves predictions for small-range species.
- **Lesson:** NLCD (30 m) will likely outperform coarser land cover data for Ohio *Ambystoma*.

### 8.3 Microclimate SDM for Amphibians
**"Microclimate species distribution models estimate lower levels of..."**
*Biological Conservation* (2023)

- Free-air coarse-resolution temperature (as in WorldClim) may overlook important microhabitat conditions.
- Microclimate data can improve SDM predictions for organismal responses, especially for small, moisture-sensitive species like salamanders.
- **Relevant:** Salamanders are highly microclimate-dependent (soil moisture, leaf litter humidity, under-canopy temperature).
- Consider downscaled climate or microclimate-corrected variables if available.

---

## 9. Ensemble Approaches (Biomod2)

biomod2 (R package) runs multiple SDM algorithms and combines them into an ensemble. Key features:
- Runs up to 10 algorithms (GLM, GBM, RF, Maxent, GAM, ANN, CART, FDA, XGBoost, etc.) on presence/pseudo-absence data.
- Combines via weighted mean, committee averaging, or other ensemble methods.
- Standard practice in modern SDM literature — reduces idiosyncratic model uncertainty.
- **Caveats:** Requires pseudo-absence generation (Maxent can work presence-background, but other algorithms in biomod2 need presence-absence). If we want ensembles, we need to think about pseudo-absence strategy.
- Python equivalent doesn't exist at the same maturity level.

---

## 10. Model Evaluation Metrics

### AUC (Area Under ROC Curve)
- Standard SDM evaluation metric since Phillips et al. (2006).
- Measures discrimination: ability to distinguish presence from background.
- **Limitations with presence-only:** Background is not true absence, so AUC interpretation differs from presence-absence context. A model can have high AUC simply by correctly identifying that presences differ from random background, even if it doesn't correctly identify habitat suitability.
- Inflated values in small study areas (Lobo et al. 2008).

### TSS (True Skill Statistic)
- = Sensitivity + Specificity - 1
- Requires a threshold to convert continuous predictions to binary.
- Less sensitive to prevalence than AUC but still threshold-dependent.

### Boyce Index
- Presence-only evaluation metric — doesn't need absences or thresholds.
- Compares predicted vs. expected frequency of occurrences across habitat suitability bins.
- Better suited for presence-only models than AUC.

### Recommendation
Report multiple metrics: AUC (for comparability with literature), TSS (for threshold-based evaluation), and Boyce index (for honest presence-only assessment). Always report both calibration and evaluation metrics, using spatially independent folds if possible.

---

## 11. Key Decision Points for Our Project

### Maxent vs RF — what the literature says
- **Maxent** is the SDM standard, designed for presence-only data, well-understood, interpretable output (habitat suitability scores). Performs best "on average" across species (Hage et al. 2026) but requires careful tuning with small samples (Morales et al. 2017).
- **RF** often outperforms Maxent in AUC in direct comparisons (Zhang et al. 2022, Mi et al. 2017), handles interactions automatically, and is robust to small samples. But RF for presence-only data requires generating pseudo-absences/background points — there's no clean "presence-only RF" the way Maxent is inherently presence-only.
- **Hisup:** RF can be adapted for presence-only by treating background points as "pseudo-absences" with class weights. This is sometimes called "presence-background RF" — less principled than Maxent's formulation but often works well empirically.
- **Ensemble (biomod2)** reduces model-specific risk but adds complexity (R dependency, pseudo-absence strategy).

- **Our recommendation:** Start with both Maxent (via elapid) and RF (via scikit-learn with pseudo-absences). Compare. If they agree, report both. If they disagree, investigate why.

### Species-level vs genus-level modeling
- Species-level: ecologically meaningful, respects different habitat niches (especially *A. barbouri* streamside vs others vernal pool).
- Genus-level: more data, but conflates species with different habitat requirements.
- **Recommend:** Model species-level if occurrence counts permit (>15–20 research-grade records per species). Aggregate to genus-level only if sample sizes are too small. Model *A. barbouri* separately regardless.

### Spatial extent
- Training on a broader region (e.g., eastern US) gives more occurrence records and environmental coverage.
- Clipping predictions to Ohio focuses the output for ODNR's use.
- **Recommend:** Train on eastern US (or Great Lakes region), predict to Ohio.

### Covariate selection
Based on *Ambystoma* ecology and SDM literature:
- **Soil:** SSURGO drainage class, hydric soil, texture class
- **Land cover:** NLCD forest type (deciduous/evergreen/mixed), wetland, developed
- **Hydrology:** NHD distance to nearest stream, distance to nearest wetland, stream density
- **Climate:** WorldClim precipitation and temperature (seasonality matters for breeding phenology)
- **Topography:** NED elevation, slope, topographic wetness index
- **Avoid:** correlated climate variables — select a meaningful subset, not all 19 bioclims

---

## Key References Summary

| # | Reference | Relevance |
|---|-----------|-----------|
| 1 | Phillips et al. 2006, *Ecol. Model.* 190:231–259 | Foundational Maxent SDM paper (in repo) |
| 2 | Phillips & Dudík 2008, *Ecography* 31:161–175 | Maxent parameter tuning, extensions |
| 3 | Elith & Leathwick 2009, *Ann. Rev. Ecol. Evol. Syst.* 40:677–697 | SDM theory review |
| 4 | Merow et al. 2013, *Ecography* 36:1058–1069 | Practical Maxent settings guide |
| 5 | Morales et al. 2017, *PeerJ* 5:e3093 | Small-sample Maxent configuration |
| 6 | Zhang et al. 2022, *STOTEN* | Maxent vs RF comparison |
| 7 | Mi et al. 2017, *PeerJ* 5:e2849 | RF for rare species with few samples |
| 8 | Hage et al. 2026, *Ecography* | Flexible methods for small samples |
| 9 | Geurts et al. 2023, *Ecosphere* | iNaturalist spatial bias |
| 10 | Callaghan et al. 2021, *BioScience* | iNaturalist observer patterns |
| 11 | Warren & Seifert 2011, *Ecography* | ENM model evaluation |
| 12 | Radosavljevic & Anderson 2014 | Species-specific Maxent tuning |
| 13 | Shcheglovitova & Anderson 2013 | Maxent small-sample parameterization |
| 14 | Aiello-Lammens et al. 2015 | spThin: spatial thinning R package |
| 15 | Zurell et al. 2022, *Landscape Ecol.* 37:1293–1314 | SDM algorithm comparison across clades |
| 16 | elapid docs | Python Maxent implementation |
| 17 | pyinaturalist docs | iNaturalist API Python client |
| 18 | Ohio ODNR Amphibians Field Guide (pub348) | Ohio *Ambystoma* species list |
| 19 | Ohio Listed Species (pub356) | Ohio endangered/threatened status |
| 20 | Jefferson salamander Illinois SDM (2012) | Direct precedent: *A. jeffersonianum* SDM for survey guidance |
