# Fit a growth trajectory

Supports transparent empirical models commonly used for plant growth.
For grouped `pheno_series` objects, one model is fitted per
subject-trait series.

## Usage

``` r
pheno_growth(
  x,
  time = NULL,
  value = NULL,
  model = c("logistic", "gompertz", "richards", "linear", "quadratic", "exponential",
    "spline", "gam"),
  group = NULL,
  start = NULL
)
```

## Arguments

- x:

  Data frame or `pheno_series`.

- time:

  Time-column name.

- value:

  Value-column name.

- model:

  Growth model: `linear`, `quadratic`, `exponential`, `logistic`,
  `gompertz`, `richards`, `spline`, or `gam`.

- group:

  Optional grouping columns. For `pheno_series`, subject and trait are
  used by default.

- start:

  Optional named starting values for nonlinear models.

## Value

A `pheno_growth` object containing fitted models and metadata.

## References

Paine CET et al. (2012). Methods in Ecology and Evolution 3:245-256.
[doi:10.1111/j.2041-210X.2011.00155.x](https://doi.org/10.1111/j.2041-210X.2011.00155.x)
.

## Examples

``` r
d <- pheno_data("growth_series")
g1 <- pheno_growth(subset(d, plant_id == "P01" & trait == "leaf_area"),
  "day", "value", "logistic")
g2 <- pheno_growth(subset(d, plant_id == "P02" & trait == "leaf_area"),
  "day", "value", "gompertz")
g3 <- pheno_growth(subset(d, plant_id == "P03" & trait == "leaf_area"), "day", "value", "spline")
```
