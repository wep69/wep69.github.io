# agriLandSuit 1.0.0

**Agricultural Land Suitability and Edaphoclimatic Zoning**

`agriLandSuit` is an R-first framework for transparent, spatially
explicit and uncertainty-aware agricultural land suitability. The 1.0.0
release consolidates the complete workflow developed from versions 0.1.0
through 0.9.0 while preserving a single public API.

## Scientific workflow

``` text
environmental layers + explicit units + crop profile
                    |
                    v
        criterion-specific suitability
        climate / soil / terrain / water
                    |
                    v
      explicit restrictions and diagnostics
        exclude / cap / penalty
                    |
                    v
        composite suitability and classes
 limiting / weighted arithmetic / weighted geometric
                    |
          +---------+---------+
          |                   |
          v                   v
    MCDA support        climate scenarios
 AHP / TOPSIS /       model / period / pathway
 weight sensitivity      / management
          |                   |
          +---------+---------+
                    |
                    v
       uncertainty and model ensembles
 Monte Carlo / P05-P50-P95 / class probabilities
                    |
                    v
          multi-crop decision layer
 rank / best / second-best / margin / P(best)
                    |
                    v
     provenance / fingerprints / portable bundles
          reports / targets / geotargets
```

## Core principles

1.  **R and `terra` are authoritative.** Python is optional and never
    required to install or use the package.
2.  **Crop evidence is separate from decision preference.** Agronomic
    requirements belong to
    [`crop_profile()`](https://wep69.github.io/agriLandSuit/reference/crop_profile.md);
    weights belong to aggregation or MCDA.
3.  **Restrictions are not criteria.** `exclude`, `cap`, and `penalty`
    remain explicit.
4.  **No hidden transformations.** The package does not silently convert
    units, resample scenarios, invert climate risk, impute missing
    suitability, or invent uncertainty distributions.
5.  **Uncertainty is retained until summarization.** Scenario, GCM, and
    Monte Carlo members remain identifiable.
6.  **Reproducibility is part of the analysis.** Fingerprints,
    provenance, manifests, and portable bundles are first-class
    infrastructure.

## Minimal deterministic workflow

``` r

library(terra)
library(agriLandSuit)

r <- rast(nrows = 20, ncols = 20, xmin = 0, xmax = 20, ymin = 0, ymax = 20,
          crs = "EPSG:31985")
values(r) <- seq(18, 34, length.out = ncell(r))
names(r) <- "temperature_mean"

land <- land_data(
  climate = r,
  units = c("climate.temperature_mean" = "degC")
)

req <- crop_requirement(
  criterion = "temperature_mean",
  domain = "climate",
  unit = "degC",
  response = "range",
  limits = c(18, 22, 28, 34),
  source = "synthetic demonstration",
  source_id = "demo-1"
)

crop <- crop_profile(
  profile_id = "demo_crop",
  scientific_name = "Synthetic crop",
  management = "rainfed",
  requirements = list(req)
)

criteria <- crop_criteria(land, crop)
result <- land_suitability(criteria, method = "limiting")
plot(result)
```

The numerical limits above are synthetic and must not be interpreted as
crop recommendations.

## Decision support

`agriLandSuit` offers three deterministic aggregation families:

- `limiting`: non-compensatory minimum, recommended as the strict
  agronomic baseline;
- `weighted_arithmetic`: compensatory weighted mean;
- `weighted_geometric`: partially compensatory composition with exact
  preservation of zero suitability.

AHP and TOPSIS are optional decision-support layers.
[`ahp_weights()`](https://wep69.github.io/agriLandSuit/reference/ahp_weights.md)
derives weights and reports consistency;
[`topsis_score()`](https://wep69.github.io/agriLandSuit/reference/topsis_score.md)
ranks alternatives. These methods do not modify the agronomic crop
profile.

## Climate scenarios and `agriClimateRisk`

[`land_scenario()`](https://wep69.github.io/agriLandSuit/reference/land_scenario.md)
and
[`scenario_set()`](https://wep69.github.io/agriLandSuit/reference/scenario_set.md)
organize scenario metadata.
[`scenario_suitability()`](https://wep69.github.io/agriLandSuit/reference/scenario_suitability.md),
[`scenario_delta()`](https://wep69.github.io/agriLandSuit/reference/scenario_delta.md),
[`scenario_transition()`](https://wep69.github.io/agriLandSuit/reference/scenario_transition.md),
and
[`scenario_area_change()`](https://wep69.github.io/agriLandSuit/reference/scenario_area_change.md)
compare aligned analyses. Geometry is never harmonized silently.

[`climate_risk_adapter()`](https://wep69.github.io/agriLandSuit/reference/climate_risk_adapter.md)
accepts already-computed external risk layers. It does not calculate
drought, heat, frost, or phenological risk, and it never assumes
`suitability = 1 - risk`. This keeps `agriLandSuit` complementary to
`agriClimateRisk`.

## Uncertainty and ensembles

[`uncertainty_spec()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_spec.md)
declares uncertainty explicitly.
[`suit_monte_carlo()`](https://wep69.github.io/agriLandSuit/reference/suit_monte_carlo.md)
propagates it;
[`ensemble_suitability()`](https://wep69.github.io/agriLandSuit/reference/ensemble_suitability.md)
preserves model or scenario members;
[`uncertainty_summary()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_summary.md),
[`class_probability()`](https://wep69.github.io/agriLandSuit/reference/class_probability.md),
[`class_stability()`](https://wep69.github.io/agriLandSuit/reference/class_stability.md),
and
[`uncertainty_decompose()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_decompose.md)
summarize the resulting uncertainty.

Suitability scores can use Beta perturbations, which preserve the \[0,
1\] support, and weights can use positive multiplicative perturbations
followed by normalization. No distribution is inferred automatically.

## Multi-crop comparison

``` text
individual crop suitability
        -> compare_crops()
        -> crop_rank()
        -> best_crop() / second_best_crop()
        -> decision_margin()

aligned uncertainty ensembles
        -> crop_winner_probability()
        -> crop_rank_stability()
```

Top-score ties remain explicit. Probabilistic ties split probability
mass equally by default.

## Reproducibility

The final engineering layer includes:

- [`land_fingerprint()`](https://wep69.github.io/agriLandSuit/reference/land_fingerprint.md)
- [`land_provenance()`](https://wep69.github.io/agriLandSuit/reference/land_provenance.md)
- [`land_manifest()`](https://wep69.github.io/agriLandSuit/reference/land_manifest.md)
- [`land_export()`](https://wep69.github.io/agriLandSuit/reference/land_export.md)
  /
  [`land_import()`](https://wep69.github.io/agriLandSuit/reference/land_import.md)
- [`external_contract()`](https://wep69.github.io/agriLandSuit/reference/external_contract.md)
- [`reproducibility_check()`](https://wep69.github.io/agriLandSuit/reference/reproducibility_check.md)
- [`suit_report()`](https://wep69.github.io/agriLandSuit/reference/suit_report.md)
- [`targets_template()`](https://wep69.github.io/agriLandSuit/reference/targets_template.md)

For large spatial projects, `targets` and `geotargets` remain optional
workflow integrations.

## Optional Python backends

Python is accessed only through `reticulate` and is never installed
automatically. Active optional computational paths at 1.0.0 are:

- NumPy + scikit-fuzzy for fuzzy numeric parity;
- PyMCDM for selected AHP/TOPSIS matrix workflows;
- NumPy `Generator` for optional numeric Monte Carlo sampling.

`xarray`, `rioxarray`, `rasterio`, `geopandas`, `shapely`, `pyproj`,
`dask`, PyMC, and ArviZ remain interoperability or advanced-extension
candidates rather than requirements of the core API.

## Vignettes

Read approximately in this order:

1.  `00-complete-workflow.Rmd`
2.  `01-foundations.Rmd`
3.  `02-criterion-engine.Rmd`
4.  `03-soil-terrain-water-constraints.Rmd`
5.  `04-composite-suitability.Rmd`
6.  `05-mcda-ahp-topsis.Rmd`
7.  `06-climate-scenarios.Rmd`
8.  `07-uncertainty-ensembles.Rmd`
9.  `08-multicrop-decision.Rmd`
10. `09-reproducibility-interoperability.Rmd`

## Validation status

The 1.0.0 assembly is statically and numerically audited in this
environment. R itself is not installed here, so executable `testthat`,
vignette rendering, `R CMD build`, and `R CMD check --as-cran` must be
completed locally. The creator/maintainer metadata now include the
supplied address `walterufpb@yahoo.com.br`; formal release certification
therefore depends only on the executable local validation steps and any
issues they reveal.
