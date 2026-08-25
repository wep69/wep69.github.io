# Align repeated phenotype observations to a common time grid

Alignment is performed independently for each subject-trait series.

## Usage

``` r
pheno_time_align(
  x,
  grid = NULL,
  interval = 1,
  method = c("linear", "nearest", "exact"),
  extrapolate = FALSE
)
```

## Arguments

- x:

  A `pheno_series`.

- grid:

  Numeric/Date time grid. If omitted, a regular numeric grid is
  generated from the observed range.

- interval:

  Numeric interval used when `grid` is omitted.

- method:

  `"linear"`, `"nearest"`, or `"exact"`.

- extrapolate:

  Allow endpoint extrapolation for linear interpolation.

## Value

A new `pheno_series` on the requested grid.

## Examples

``` r
s <- pheno_series(pheno_data("growth_series"), "plant_id", "day", "trait", "value")
a1 <- pheno_time_align(s, interval = 2)
a2 <- pheno_time_align(s, grid = seq(0, 28, by = 4), method = "nearest")
a3 <- pheno_time_align(s, grid = sort(unique(s$day)), method = "exact")
```
