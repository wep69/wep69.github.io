# Mark quality-control conditions in a longitudinal series

Adds a quality flag rather than silently deleting observations.

## Usage

``` r
pheno_series_qc(
  x,
  flag_column = "quality_flag",
  duplicate_flag = "duplicate",
  missing_flag = "missing",
  nonfinite_flag = "nonfinite"
)
```

## Arguments

- x:

  A `pheno_series`.

- flag_column:

  Output quality-flag column.

- duplicate_flag:

  Label for duplicated subject-time-trait observations.

- missing_flag:

  Label for missing values.

- nonfinite_flag:

  Label for non-finite values.

## Value

A `pheno_series` with an added/updated quality flag.

## Examples

``` r
s <- pheno_series(pheno_data("growth_series"), "plant_id", "day", "trait", "value")
q1 <- pheno_series_qc(s); table(q1$quality_flag)
#> 
#>  OK 
#> 120 
q2 <- pheno_series_qc(s, flag_column = "qc2")
head(pheno_series_qc(s, missing_flag = "missing_measurement"))
#> <pheno_series>
#>   rows: 6 
#>   subjects: 2 
#>   traits: 1 
#>   time range: 0 to 28 
```
