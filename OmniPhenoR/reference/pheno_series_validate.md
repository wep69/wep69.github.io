# Validate longitudinal identity and time structure

Detects duplicated subject-time-trait records, missing identity,
non-finite values, reversed within-subject time order, and inconsistent
unit labels.

## Usage

``` r
pheno_series_validate(
  x,
  allow_duplicates = FALSE,
  require_complete_identity = TRUE
)
```

## Arguments

- x:

  A `pheno_series`.

- allow_duplicates:

  Whether duplicate subject-time-trait observations are acceptable.

- require_complete_identity:

  Require non-missing subject, time and trait.

## Value

A tibble of validation checks with a `pass` column.

## Examples

``` r
s <- pheno_series(pheno_data("growth_series"), "plant_id", "day", "trait", "value")
pheno_series_validate(s)
#> # A tibble: 4 × 3
#>   check                        pass  detail
#>   <chr>                        <lgl> <chr> 
#> 1 duplicate subject-time-trait TRUE  FALSE 
#> 2 complete identity            TRUE  FALSE 
#> 3 finite numeric values        TRUE  TRUE  
#> 4 within-series order          TRUE  TRUE  
pheno_series_validate(s, allow_duplicates = TRUE)
#> # A tibble: 4 × 3
#>   check                        pass  detail
#>   <chr>                        <lgl> <chr> 
#> 1 duplicate subject-time-trait TRUE  FALSE 
#> 2 complete identity            TRUE  FALSE 
#> 3 finite numeric values        TRUE  TRUE  
#> 4 within-series order          TRUE  TRUE  
all(pheno_series_validate(s)$pass)
#> [1] TRUE
```
