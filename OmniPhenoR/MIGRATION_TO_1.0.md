# OmniPhenoR 1.0.0 Migration and API Consolidation Guide

## Purpose

Version 1.0.0 is the first consolidated stable interface. It was
constructed by comparing the public namespace and implementation surface
of 0.1.0, 0.2.0, 0.3.0, 0.4.0, and 0.5.0 rather than simply renaming the
0.5.0 source.

## Cross-version result

The integrated audit found **no public export removals from 0.1.0
through 0.4.0**. The 0.5.0 development source removed eight exports that
were present in 0.4.0:

``` text
pheno_correlation_structures
pheno_curve_compare
pheno_event
pheno_event_response
pheno_growth
pheno_model_diagnostics
pheno_phenology
pheno_series_qc
```

It also lost these S3 methods:

``` text
print.pheno_functional
print.pheno_growth
print.pheno_repeated_fit
```

Those functions/methods are restored in 1.0.0.

The audit also detected **28 overlapping function signatures that
changed between the 0.4.0 and 0.5.0 development sources**. Most were
longitudinal functions. Because the 0.4.0 interfaces generally support
grouped data, `pheno_series`, richer model choices, and explicit
experimental structure, they are retained as the canonical 1.0.0
interfaces for overlapping names.

## 0.5-only convenience entry points retained

The following non-conflicting entry points remain available:

- [`pheno_gdd()`](https://wep69.github.io/OmniPhenoR/reference/pheno_gdd.md)
- [`pheno_interpolate()`](https://wep69.github.io/OmniPhenoR/reference/pheno_interpolate.md)
- [`pheno_growth_fit()`](https://wep69.github.io/OmniPhenoR/reference/pheno_growth_fit.md)
- [`pheno_growth_predict()`](https://wep69.github.io/OmniPhenoR/reference/pheno_growth_predict.md)
- [`pheno_audpc()`](https://wep69.github.io/OmniPhenoR/reference/pheno_audpc.md)
- [`pheno_events()`](https://wep69.github.io/OmniPhenoR/reference/pheno_events.md)
- [`pheno_time_gaps()`](https://wep69.github.io/OmniPhenoR/reference/pheno_time_gaps.md)
- [`pheno_time_since_event()`](https://wep69.github.io/OmniPhenoR/reference/pheno_time_since_event.md)
- [`pheno_missing_time()`](https://wep69.github.io/OmniPhenoR/reference/pheno_missing_time.md)
- [`pheno_time_validate_model()`](https://wep69.github.io/OmniPhenoR/reference/pheno_time_validate_model.md)
- [`pheno_curve_distance()`](https://wep69.github.io/OmniPhenoR/reference/pheno_curve_distance.md)

All experiment/data-infrastructure functions introduced in 0.5.0 are
also retained.

## Stable longitudinal examples

### Canonical grouped growth workflow

``` r

d <- subset(pheno_data("growth_series"), trait == "leaf_area")

fit <- pheno_growth(
  d,
  time = "day",
  value = "value",
  model = "logistic",
  group = "plant_id"
)

pheno_growth_traits(fit)
```

### Compact single-trajectory convenience workflow

``` r

time <- 0:10
value <- 100 / (1 + exp(-(time - 5)))

fit <- pheno_growth_fit(time, value, "logistic")
pheno_growth_predict(fit, seq(0, 10, by = 0.25))
```

These workflows coexist intentionally.

## Exact public-API ledger

See `inst/metadata/api_compatibility_1.0.csv`.

The 1.0.0 namespace contains **223 public exports**, equal to the union
of all exports observed in the five preceding development generations.

## Release rule

Do not remove or silently repurpose a 1.0.0 public function in a patch
release. Future incompatible API changes belong in a documented
major-version migration.
