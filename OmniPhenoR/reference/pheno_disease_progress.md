# Summarize longitudinal disease progress

Summarize longitudinal disease progress

## Usage

``` r
pheno_disease_progress(data, time, severity, group = NULL, onset_threshold = 5)
```

## Arguments

- data:

  Data frame containing disease observations.

- time:

  Time column.

- severity:

  Severity column.

- group:

  Optional grouping columns, typically plant, plot, treatment, or
  genotype.

- onset_threshold:

  Severity threshold used to define onset.

## Value

A tibble with AUDPC, standardized AUDPC, maximum severity, onset and
maximum slope.

## Examples

``` r
d <- pheno_data("disease_series")
pheno_disease_progress(d, "day", "severity", group = "plant_id")
#> # A tibble: 12 × 6
#>    series audpc standardized_audpc max_severity onset max_rate
#>    <chr>  <dbl>              <dbl>        <dbl> <dbl>    <dbl>
#>  1 D01    1032.               36.9         84.4     7     4.67
#>  2 D02    1007.               35.9         88.2     7     5.87
#>  3 D03     999.               35.7         87.6    14     5.39
#>  4 D04    1004.               35.8         86.9     7     4.96
#>  5 D05    1038.               37.1         92.2     7     5.05
#>  6 D06    1014.               36.2         88.1     7     5.52
#>  7 D07     475.               17.0         47.7     0     3.09
#>  8 D08     519.               18.5         47.8     0     2.73
#>  9 D09     425.               15.2         46.8    14     3.38
#> 10 D10     442.               15.8         45.2    14     3.09
#> 11 D11     504.               18.0         47.1     7     2.91
#> 12 D12     504.               18.0         47.4     7     2.82
pheno_disease_progress(
  d, "day", "severity", group = c("genotype", "plant_id"), onset_threshold = 10
)
#> # A tibble: 12 × 6
#>    series audpc standardized_audpc max_severity onset max_rate
#>    <chr>  <dbl>              <dbl>        <dbl> <dbl>    <dbl>
#>  1 G1.D01 1032.               36.9         84.4    14     4.67
#>  2 G1.D02 1007.               35.9         88.2    14     5.87
#>  3 G1.D03  999.               35.7         87.6    14     5.39
#>  4 G1.D04 1004.               35.8         86.9    14     4.96
#>  5 G1.D05 1038.               37.1         92.2    14     5.05
#>  6 G1.D06 1014.               36.2         88.1    14     5.52
#>  7 G2.D07  475.               17.0         47.7    14     3.09
#>  8 G2.D08  519.               18.5         47.8    14     2.73
#>  9 G2.D09  425.               15.2         46.8    21     3.38
#> 10 G2.D10  442.               15.8         45.2    14     3.09
#> 11 G2.D11  504.               18.0         47.1    14     2.91
#> 12 G2.D12  504.               18.0         47.4    14     2.82
pheno_disease_progress(subset(d, genotype == "G1"), "day", "severity", group = "plant_id")
#> # A tibble: 6 × 6
#>   series audpc standardized_audpc max_severity onset max_rate
#>   <chr>  <dbl>              <dbl>        <dbl> <dbl>    <dbl>
#> 1 D01    1032.               36.9         84.4     7     4.67
#> 2 D02    1007.               35.9         88.2     7     5.87
#> 3 D03     999.               35.7         87.6    14     5.39
#> 4 D04    1004.               35.8         86.9     7     4.96
#> 5 D05    1038.               37.1         92.2     7     5.05
#> 6 D06    1014.               36.2         88.1     7     5.52
```
