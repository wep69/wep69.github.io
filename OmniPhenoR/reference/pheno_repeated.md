# Fit a repeated-measures phenotype model

The function preserves subject identity and exposes the residual
correlation assumption. `nlme` is preferred when correlation structures
are required; `lme4` and an explicit fixed-subject `lm` fallback are
also available.

## Usage

``` r
pheno_repeated(
  data,
  response,
  time,
  treatment = NULL,
  subject,
  block = NULL,
  engine = c("nlme", "lme4", "lm"),
  correlation = c("independence", "compound_symmetry", "ar1", "car1", "gaussian",
    "exponential"),
  random_slope = TRUE,
  method = c("REML", "ML")
)
```

## Arguments

- data:

  Data frame or `pheno_series`.

- response:

  Response column.

- time:

  Time column.

- treatment:

  Optional treatment/group column.

- subject:

  Subject identifier.

- block:

  Optional block column.

- engine:

  `"nlme"`, `"lme4"`, or `"lm"`.

- correlation:

  Residual correlation structure.

- random_slope:

  Include a random time slope when supported.

- method:

  `"REML"` or `"ML"` for mixed models.

## Value

A `pheno_repeated_fit` object.

## References

Pinheiro JC, Bates DM (2000). *Mixed-Effects Models in S and S-PLUS*.
[doi:10.1007/b98882](https://doi.org/10.1007/b98882) .

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
m1 <- pheno_repeated(d, "value", "day", "treatment", "plant_id", engine = "lm")
m2 <- pheno_repeated(d, "value", "day", subject = "plant_id", engine = "lm")
if (requireNamespace("nlme", quietly = TRUE)) {
  m3 <- pheno_repeated(
    d, "value", "day", "treatment", "plant_id",
    engine = "nlme", correlation = "ar1"
  )
}
```
