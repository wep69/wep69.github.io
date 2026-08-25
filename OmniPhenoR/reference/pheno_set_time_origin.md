# Define an explicit time origin

Define an explicit time origin

## Usage

``` r
pheno_set_time_origin(x, origin, new_time = "relative_time", by_subject = TRUE)
```

## Arguments

- x:

  A `pheno_series`.

- origin:

  Numeric/Date origin or a column containing event times.

- new_time:

  Name of the derived relative-time column.

- by_subject:

  Apply a scalar origin separately to each subject; retained for
  explicit provenance and future extensions.

## Value

A `pheno_series` with an added relative-time column.

## Examples

``` r
s <- pheno_series(pheno_data("growth_series"), "plant_id", "day", "trait", "value")
pheno_set_time_origin(s, 7, "days_after_treatment")
#> <pheno_series>
#>   rows: 120 
#>   subjects: 12 
#>   traits: 2 
#>   time range: 0 to 28 
pheno_set_time_origin(s, 0, "days_after_emergence")
#> <pheno_series>
#>   rows: 120 
#>   subjects: 12 
#>   traits: 2 
#>   time range: 0 to 28 
pheno_set_time_origin(s, min(s$day), "relative_day", by_subject = FALSE)
#> <pheno_series>
#>   rows: 120 
#>   subjects: 12 
#>   traits: 2 
#>   time range: 0 to 28 
```
