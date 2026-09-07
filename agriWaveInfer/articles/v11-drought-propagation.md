# Multiscale Drought Propagation from Climate to Crop Response

## Propagation is not a fixed universal chain

Recent studies show that meteorological to agricultural drought
propagation varies with initial soil moisture, vegetation, irrigation,
season and region (Xu et al. 2026; Sun et al. 2025). Global analyses
also report sequential, reverse and leapfrogging pathways, which argues
against assuming one fixed pathway everywhere (Long et al. 2026).

`agriWaveInfer` therefore asks the researcher to state the hypothesized
order and then tests whether adjacent multiscale phase relationships
support it.

## Harmonize indicator signs first

A precipitation anomaly and an SPEI often become more negative during
drought, whereas a drought-severity index may become more positive. A
phase reversal caused only by sign convention is not a biological
mechanism.

``` r

chain <- awi_drought_chain(
  series = list(precip, soil_moisture, ndvi, yield),
  roles = c("precipitation", "soil_moisture", "NDVI", "yield"),
  orientation = rep("wet_positive", 4),
  target_orientation = "drought_positive"
)
chain$sign_harmonization
```

The raw series are retained and every multiplication by minus one is
recorded.

## Define propagation evidence

``` r

ps <- awi_propagation_spec(
  require_significance = TRUE,
  min_coherence = 0.4,
  min_resultant_length = 0.5,
  min_direction_persistence = 0.6,
  allow_reverse = FALSE,
  nrands = 199
)
```

Each adjacent edge must show adequate coherence, circular concentration
and persistence in the expected direction. If significance is required,
the WTC significance layer is also applied.

## Evaluate an agronomic chain

``` r

p <- awi_propagation(
  chain,
  period_range = c(20, 100),
  spec = ps
)
p$table

lag <- awi_propagation_lag(p)
lag$cumulative_lag
```

A cumulative lag is reported only when all adjacent edges satisfy the
selected rule. It is the sum of adjacent principal-branch phase
summaries and should be interpreted as a descriptive multiscale timing
summary.

## What a defensible conclusion looks like

A suitable statement is that precipitation stress preceded soil-moisture
stress, which in turn preceded vegetation and yield responses in a
specified coherent period band, with reported persistence and lag
estimates. The package does not convert this sequence into a causal
claim. Event-scale studies have shown that propagation time can depend
strongly on antecedent soil moisture, while broader analyses show that
pathway order can change between regions (Xu et al. 2026; Long et al.
2026).

Long, Junchen, Changchun Xu, Hongyu Wang, Zhiyi Li, and Fujin Xu. 2026.
“Reconstruction of Drought Propagation Pathways: A Global Analysis of
Multitype Propagation Chains and Nonlinear Mechanisms.” *Global and
Planetary Change* 256: 105144.
<https://doi.org/10.1016/j.gloplacha.2025.105144>.

Sun, Peng, Ruilin Liu, Rui Yao, et al. 2025. “Propagation Threshold from
Meteorological to Agricultural Drought and Its Potential Influence
Factors.” *Journal of Hydrology* 655: 132920.
<https://doi.org/10.1016/j.jhydrol.2025.132920>.

Xu, Zhengguang, Bo Jiang, Xiao Guo, Zhiyong Wu, and Siqi Fan. 2026.
“Initial Soil Moisture Conditions Dominate Variation in Event-Scale
Propagation Time from Meteorological to Agricultural Drought.”
*Agricultural Water Management* 325: 110128.
<https://doi.org/10.1016/j.agwat.2026.110128>.
