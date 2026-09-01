# Climate scenarios and edaphoclimatic change

## Purpose

Version 0.6.0 evaluates the same agronomic suitability model across
explicitly described baseline and future land-data scenarios. Scenario
metadata do not alter crop requirements or decision weights.

## Scenario contract

``` r

baseline <- land_scenario(
  land_baseline, "baseline", model = "observed",
  period = "1991-2020", management = "rainfed", baseline = TRUE
)
future <- land_scenario(
  land_future, "ssp245_2050", model = "GCM_A",
  period = "2041-2070", management = "rainfed", pathway = "SSP2-4.5"
)
scenarios <- scenario_set(baseline, future)
```

[`scenario_set()`](https://wep69.github.io/agriLandSuit/reference/scenario_set.md)
does not resample. Geometry mismatch is an error, so every change map is
based on cell-to-cell comparable land data.

## Suitability and change

``` r

ss <- scenario_suitability(scenarios, crop, method = "limiting")
delta <- scenario_delta(ss)
transition <- scenario_transition(ss)
area <- scenario_area_change(transition, unit = "ha")
```

[`scenario_delta()`](https://wep69.github.io/agriLandSuit/reference/scenario_delta.md)
quantifies continuous-score change.
[`scenario_transition()`](https://wep69.github.io/agriLandSuit/reference/scenario_transition.md)
encodes transitions among the same suitability classes, and
[`scenario_area_change()`](https://wep69.github.io/agriLandSuit/reference/scenario_area_change.md)
summarizes their mapped extent.

## Interoperability with agriClimateRisk

`agriLandSuit` does not duplicate climate-risk calculations. A
precomputed external object can be adapted using an explicit extractor.

``` r

land_from_risk <- climate_risk_adapter(
  risk_object,
  extractor = function(x) x$risk_layers,
  role = "constraint_source"
)
```

No automatic relation such as `suitability = 1 - risk` is assumed. If a
risk product is used as a criterion or restriction, its agronomic
interpretation must be defined explicitly by the analyst.

## What remains outside 0.6.0

Monte Carlo propagation, GCM weighting, probabilistic class membership,
ensemble uncertainty, and variance decomposition are intentionally
deferred to 0.7.0.
