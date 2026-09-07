# Parsimonious multiscale driver selection

## Goal

Multiple wavelet coherence can evaluate many predictor combinations, but
the number of subsets grows rapidly. More importantly, maximizing
coherence without a parsimony rule can reward redundant drivers and
numerically unstable spectral matrices. Version 0.4.0 introduces a
formal driver-selection layer that combines coherence summaries,
incremental gain and local conditioning diagnostics.

## Policy object

Selection decisions are encoded in `AwiDriverSpec` rather than scattered
across function arguments:

``` r

policy <- awi_driver_spec(
  metric = "posc",
  min_gain = 5,
  max_drivers = 4,
  strategy = "cea",
  weighting = "area",
  max_unstable_fraction = 0.10,
  conditional_check = "pwc"
)
```

The five-point default reflects a recent agricultural CEA application
(Sankaran et al. 2026). It is a configurable decision rule, not a
universal probability threshold.

## Step 1: screen individual drivers

``` r

screen <- awi_driver_screen(
  y = yield,
  drivers = list(
    ENSO = enso,
    PDO = pdo,
    IOD = iod,
    rainfall = rain,
    temperature = temp
  ),
  null = awi_null_spec(
    method = "iaaft",
    n_surrogates = 499,
    seed = 2040,
    field_method = "BY"
  ),
  driver_spec = policy,
  period_range = c(24, 96)
)
```

The ranking table contains AWC, PoSC, the primary metric, valid-domain
fraction, unstable fraction, regularized fraction and maximum local
condition number. A high metric does not automatically make a driver
eligible if numerical diagnostics are poor.

## Step 2A: Coherence Elimination Approach

The forward strategy starts with the best eligible single driver. At
each step it evaluates every remaining candidate appended to the
retained set and keeps the best addition only when

``` math
\Delta M \ge \delta,
```

where $`M`$ is the selected metric and $`\delta`$ is `min_gain`.

``` r

fit <- awi_coherence_elimination(
  yield,
  drivers,
  null = null,
  driver_spec = policy,
  period_range = c(24, 96)
)

fit$history
fit$candidate_history
fit$conditional_support
```

This formalizes the structured addition/elimination idea used in recent
crop-climate analysis (Sankaran et al. 2026), while adding explicit
condition diagnostics and optional PWC checks.

## Step 2B: bounded exhaustive search

For a small candidate set, exhaustive subset comparison can be
scientifically useful:

``` r

policy2 <- awi_driver_spec(
  metric = "posc",
  min_gain = 5,
  max_drivers = 4,
  strategy = "exhaustive",
  max_combinations = 500
)

fit2 <- awi_mwc_select(
  yield,
  drivers,
  null = null,
  driver_spec = policy2
)
```

The combination count is bounded explicitly. The selected multi-driver
set must improve on the best single driver by at least `min_gain`;
otherwise the single-driver solution is retained.

## Redundant drivers

A predictor can be physically meaningful yet nearly redundant with
another predictor.
[`awi_mwc()`](https://wep69.github.io/agriWaveInfer/reference/awi_mwc.md)
already exposes local condition number, numerical rank and
regularization. Version 0.4.0 propagates those diagnostics to selection.
A candidate set can be rejected when too much of the target domain is
numerically unstable.

This is particularly important for correlated climate indices or
multiple meteorological variables that encode overlapping large-scale
forcing. Multiscale hydrologic studies have shown the value of comparing
combinations of ENSO, NAO and PDO (Nalley et al. 2019), but selection
still requires a transparent complexity rule.

## Stability benchmark

The source package includes
`inst/validation/driver_selection_stability.R`, a deterministic
truth-known simulation. It repeatedly creates one primary driver, a
near-duplicate driver and an independent noise driver, then records how
often the intended driver is selected first and how often noise is
spuriously appended. The benchmark is a runtime gate and is never marked
as passed until executed in R.

## What the result means

A selected set is a parsimonious multiscale association model under the
chosen metric, domain, null model and numerical policy. It is not a
causal graph and it is not automatically an out-of-sample forecasting
model.

## References

Nalley, D., J. Adamowski, A. Biswas, B. Gharabaghi, and W. Hu. 2019. “A
Multiscale and Multivariate Analysis of Precipitation and Streamflow
Variability in Relation to ENSO, NAO and PDO.” *Journal of Hydrology*
574: 288–307. <https://doi.org/10.1016/j.jhydrol.2019.04.024>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.
