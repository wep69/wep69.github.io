# Treatment contrasts at selected times

Native contrasts use observed/aligned data and return group means and
pairwise differences. If a `pheno_repeated_fit` is supplied and
`emmeans` is installed, model-based estimated marginal means can be
requested.

## Usage

``` r
pheno_time_contrasts(
  data,
  response = NULL,
  time = NULL,
  treatment = NULL,
  at,
  method = c("observed", "emmeans"),
  adjust = "tukey"
)
```

## Arguments

- data:

  Data frame, `pheno_series`, or `pheno_repeated_fit`.

- response:

  Response column for raw-data contrasts.

- time:

  Time column.

- treatment:

  Treatment column.

- at:

  Numeric times of interest.

- method:

  `"observed"` or `"emmeans"`.

- adjust:

  Multiplicity adjustment passed to `emmeans` when used.

## Value

A list of means and contrasts, or an `emmeans` contrast object.

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
pheno_time_contrasts(d,"value","day","treatment",at=c(14,28))
#> $means
#> # A tibble: 6 × 3
#>   treatment  mean  time
#>   <chr>     <dbl> <dbl>
#> 1 control    70.0    14
#> 2 drought    65.9    14
#> 3 nitrogen   75.4    14
#> 4 control   115.     28
#> 5 drought   105.     28
#> 6 nitrogen  121.     28
#> 
#> $contrasts
#> # A tibble: 6 × 3
#>    time contrast           difference
#>   <dbl> <chr>                   <dbl>
#> 1    14 control - drought        4.11
#> 2    14 control - nitrogen      -5.39
#> 3    14 drought - nitrogen      -9.50
#> 4    28 control - drought        9.96
#> 5    28 control - nitrogen      -5.90
#> 6    28 drought - nitrogen     -15.9 
#> 
pheno_time_contrasts(d,"value","day","treatment",at=21)
#> $means
#> # A tibble: 3 × 3
#>   treatment  mean  time
#>   <chr>     <dbl> <dbl>
#> 1 control   104.     21
#> 2 drought    95.0    21
#> 3 nitrogen  113.     21
#> 
#> $contrasts
#> # A tibble: 3 × 3
#>    time contrast           difference
#>   <dbl> <chr>                   <dbl>
#> 1    21 control - drought        8.66
#> 2    21 control - nitrogen      -9.17
#> 3    21 drought - nitrogen     -17.8 
#> 
fit <- pheno_repeated(d, "value", "day", "treatment", "plant_id", engine = "lm")
pheno_time_contrasts(d, "value", "day", "treatment", at = 14)
#> $means
#> # A tibble: 3 × 3
#>   treatment  mean  time
#>   <chr>     <dbl> <dbl>
#> 1 control    70.0    14
#> 2 drought    65.9    14
#> 3 nitrogen   75.4    14
#> 
#> $contrasts
#> # A tibble: 3 × 3
#>    time contrast           difference
#>   <dbl> <chr>                   <dbl>
#> 1    14 control - drought        4.11
#> 2    14 control - nitrogen      -5.39
#> 3    14 drought - nitrogen      -9.50
#> 
```
