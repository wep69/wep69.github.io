# Calculate growing degree-days

Calculate growing degree-days

## Usage

``` r
pheno_gdd(
  tmin,
  tmax,
  base = 0,
  upper = NULL,
  method = c("mean_clip", "individual_clip"),
  cumulative = TRUE
)
```

## Arguments

- tmin, tmax:

  Daily minimum and maximum temperature.

- base:

  Base temperature.

- upper:

  Optional upper threshold.

- method:

  `mean_clip` or `individual_clip`.

- cumulative:

  Return accumulated values.

## Value

Numeric vector.

## Examples

``` r
pheno_gdd(c(8,9,12), c(20,22,25), base=10)
#> [1]  4.0  9.5 18.0
pheno_gdd(c(8,9,12), c(20,22,25), base=10, method="individual_clip")
#> [1]  5.0 11.0 19.5
pheno_gdd(c(8,9,12), c(20,35,25), base=10, upper=30, cumulative=FALSE)
#> [1] 4.0 9.5 8.5
```
