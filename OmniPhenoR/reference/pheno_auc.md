# Integrate a phenotype trajectory with the trapezoidal rule

Integrate a phenotype trajectory with the trapezoidal rule

## Usage

``` r
pheno_auc(
  x,
  time = NULL,
  value = NULL,
  from = NULL,
  to = NULL,
  standardized = FALSE
)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

- from:

  Optional lower integration bound.

- to:

  Optional upper integration bound.

- standardized:

  Divide AUC by the integration duration.

## Value

Numeric area under the curve.

## References

Shaner G, Finney RE (1977). Phytopathology 67:1051-1056.
[doi:10.1094/Phyto-67-1051](https://doi.org/10.1094/Phyto-67-1051) .

## Examples

``` r
d <- subset(pheno_data("disease_series"), plant_id == "D01")
pheno_auc(d, "day", "severity")
#> [1] 1032.053
pheno_auc(d, "day", "severity", standardized = TRUE)
#> [1] 36.85904
pheno_auc(d, "day", "severity", from = 7, to = 21)
#> [1] 479.8262
```
