# agriLandSuit 1.0.0: Complete Scientific Workflow

## Purpose

This vignette links the complete 1.0.0 workflow. The included
demonstration data are synthetic and are not crop recommendations.
Replace them with locally supported crop evidence, measured or curated
environmental layers, and explicit uncertainty assumptions in applied
work.

## 1. Construct aligned environmental layers

``` r

grid <- read.csv(system.file("extdata", "demo_environment_grid.csv", package = "agriLandSuit"))

template <- rast(nrows = 8, ncols = 10, xmin = 499.5, xmax = 509.5,
                 ymin = 9199.5, ymax = 9207.5, crs = "EPSG:31985")

make_layer <- function(values, name) {
  z <- template
  values(z) <- values
  names(z) <- name
  z
}

climate <- c(
  make_layer(grid$temperature_mean_degC, "temperature_mean"),
  make_layer(grid$precipitation_mm_year, "precipitation")
)
soil <- c(
  make_layer(grid$soil_depth_cm, "soil_depth"),
  make_layer(grid$soil_ph, "soil_ph")
)
terrain <- make_layer(grid$slope_percent, "slope")
water <- make_layer(grid$water_availability_fraction, "water_availability")

land <- land_data(
  climate = climate,
  soil = soil,
  terrain = terrain,
  water = water,
  units = c(
    "climate.temperature_mean" = "degC",
    "climate.precipitation" = "mm/year",
    "soil.soil_depth" = "cm",
    "soil.soil_ph" = "pH",
    "terrain.slope" = "percent_slope",
    "water.water_availability" = "fraction"
  ),
  metadata = list(dataset = "synthetic 1.0.0 demonstration")
)

land_validate(land, strict = TRUE, require_units = TRUE)
#> <agri_validation> OK
#> No issues detected.
```

## 2. Define a sourceable crop profile

``` r

req_temp <- crop_requirement(
  "temperature_mean", "climate", "degC", "range",
  limits = c(18, 22, 28, 35),
  source = "synthetic demonstration", source_id = "demo-A-temp"
)
req_prec <- crop_requirement(
  "precipitation", "climate", "mm/year", "range",
  limits = c(650, 900, 1400, 1800),
  source = "synthetic demonstration", source_id = "demo-A-precip"
)
req_depth <- crop_requirement(
  "soil_depth", "soil", "cm", "increasing",
  limits = c(30, 70),
  source = "synthetic demonstration", source_id = "demo-A-depth"
)
req_slope <- crop_requirement(
  "slope", "terrain", "percent_slope", "decreasing",
  limits = c(5, 25),
  source = "synthetic demonstration", source_id = "demo-A-slope"
)

crop_a <- crop_profile(
  "demo_cereal_A", "Synthetic crop A", management = "rainfed",
  requirements = list(req_temp, req_prec, req_depth, req_slope),
  profile_version = "1.0-demo"
)

crop_validate(crop_a, require_sources = TRUE)
#> <agri_validation> OK
#> No issues detected.
```

## 3. Score each criterion

``` r

criteria_a <- crop_criteria(land, crop_a, engine = "r", strict_units = TRUE)
criterion_scores(criteria_a)
#> class       : SpatRaster
#> size        : 8, 10, 4  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 499.5, 509.5, 9199.5, 9207.5  (xmin, xmax, ymin, ymax)
#> coord. ref. : SIRGAS 2000 / UTM zone 25S (EPSG:31985)
#> source(s)   : memory
#> names       : temperature_mean, precipitation, soil_depth, slope
#> min values  :              0.5,           0.6,      0.125,  0.24
#> max values  :                1,             1,          1,     1
```

Inspect the response curve before accepting a threshold definition.

``` r

head(criterion_curve(req_temp))
#>    value suitability
#> 1 16.300           0
#> 2 16.402           0
#> 3 16.504           0
#> 4 16.606           0
#> 5 16.708           0
#> 6 16.810           0
```

## 4. Add explicit land restrictions

Restrictions are distinct from crop suitability. Here a steep-slope rule
excludes cells only after a threshold is exceeded.

``` r

rules <- constraint_set(
  land_constraint("steep", "terrain.slope", "exclude", "gt", threshold = 16)
)
effects <- constraint_effects(constraint_evaluate(land, rules))
```

## 5. Produce deterministic suitability

``` r

result_a <- land_suitability(
  criteria_a,
  method = "limiting",
  constraints = effects,
  breaks = c(0, .25, .50, .75, 1),
  labels = c("N", "S3", "S2", "S1")
)
summary(result_a)
#>     method n_criteria   min      mean    max constrained
#> 1 limiting          4 0.125 0.5729779 0.8625        TRUE
limiting_factor(criteria_a)
#> <agri_limiting_factor>
#>  criteria: temperature_mean, precipitation, soil_depth, slope 
#>  missing : propagate
```

## 6. Optional MCDA weights

``` r

pairwise <- ahp_matrix(
  c("temperature_mean", "precipitation", "soil_depth", "slope"),
  comparisons = data.frame(
    criterion1 = c("temperature_mean", "temperature_mean", "temperature_mean",
                   "precipitation", "precipitation", "soil_depth"),
    criterion2 = c("precipitation", "soil_depth", "slope",
                   "soil_depth", "slope", "slope"),
    value = c(1, 2, 4, 2, 4, 2)
  )
)
ahp <- ahp_weights(pairwise)
ahp
#> <agri_ahp> engine: r  method: eigen  CR: -1.645e-16 
#> temperature_mean    precipitation       soil_depth            slope 
#>       0.36363636       0.36363636       0.18181818       0.09090909
```

AHP weights describe a decision model, not the crop’s biological
requirement profile.

## 7. Construct a future climate scenario

``` r

future_climate <- c(
  make_layer(grid$future_temperature_mean_degC, "temperature_mean"),
  make_layer(grid$future_precipitation_mm_year, "precipitation")
)
future_land <- land_data(
  climate = future_climate,
  soil = soil,
  terrain = terrain,
  water = water,
  units = land_units(land),
  metadata = list(dataset = "synthetic future demonstration")
)

baseline <- land_scenario(land, "baseline", model = "observed",
                          period = "baseline", management = "rainfed",
                          baseline = TRUE)
future <- land_scenario(future_land, "future", model = "demo_gcm",
                        period = "future", management = "rainfed",
                        pathway = "illustrative")
ss <- scenario_suitability(scenario_set(baseline, future), crop_a,
                           method = "limiting")
scenario_delta(ss)
#> <agri_scenario_delta> 1 comparison(s); baseline: baseline ; type: absolute
scenario_transition(ss)
#> <agri_scenario_transition> 1 comparison(s); baseline: baseline
```

## 8. Preserve uncertainty members before summarizing

For applied work, create Monte Carlo or multi-model members from
documented uncertainty assumptions.
[`ensemble_suitability()`](https://wep69.github.io/agriLandSuit/reference/ensemble_suitability.md)
retains member identities; use
[`uncertainty_summary()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_summary.md),
[`class_probability()`](https://wep69.github.io/agriLandSuit/reference/class_probability.md),
and
[`class_stability()`](https://wep69.github.io/agriLandSuit/reference/class_stability.md)
only after the ensemble is defined.

## 9. Compare crops only after individual analyses are compatible

Use
[`compare_crops()`](https://wep69.github.io/agriLandSuit/reference/compare_crops.md),
[`crop_rank()`](https://wep69.github.io/agriLandSuit/reference/crop_rank.md),
[`best_crop()`](https://wep69.github.io/agriLandSuit/reference/best_crop.md),
[`second_best_crop()`](https://wep69.github.io/agriLandSuit/reference/second_best_crop.md),
and
[`decision_margin()`](https://wep69.github.io/agriLandSuit/reference/decision_margin.md)
on individually generated crop suitability results. If aligned ensembles
are available, use
[`crop_winner_probability()`](https://wep69.github.io/agriLandSuit/reference/crop_winner_probability.md)
and
[`crop_rank_stability()`](https://wep69.github.io/agriLandSuit/reference/crop_rank_stability.md)
rather than ranking only the ensemble means.

## 10. Freeze provenance and exchange the result

``` r

fp <- land_fingerprint(result_a)
prov <- land_provenance(result_a, parameters = list(method = "limiting"))
manifest <- land_manifest(result_a, parameters = list(method = "limiting"))
fp
#> <agri_fingerprint> 6d6fdf3ef4f53e59dc46014823483c5287303ffd29843df422d55606d4fba24c
manifest
#> <agri_run_manifest>
#>  schema: agriLandSuit-run-manifest/1 
#>  fingerprint: 6d6fdf3ef4f53e59dc46014823483c5287303ffd29843df422d55606d4fba24c
```

Use
[`land_export()`](https://wep69.github.io/agriLandSuit/reference/land_export.md)
for portable bundles,
[`reproducibility_check()`](https://wep69.github.io/agriLandSuit/reference/reproducibility_check.md)
when re-opening an analysis,
[`suit_report()`](https://wep69.github.io/agriLandSuit/reference/suit_report.md)
for lightweight reporting, and
[`targets_template()`](https://wep69.github.io/agriLandSuit/reference/targets_template.md)
to initialize a reproducible pipeline.

## Recommended next reading

Continue with vignettes 01 through 09 for the detailed scientific
contracts behind each block of the complete workflow.
