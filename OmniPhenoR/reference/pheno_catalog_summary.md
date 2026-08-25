# Summarize an experiment catalog

Summarize an experiment catalog

## Usage

``` r
pheno_catalog_summary(catalog)
```

## Arguments

- catalog:

  `pheno_catalog`.

## Value

One-row tibble.

## Examples

``` r
c<-pheno_catalog(file.path(tempdir(),"cat_sum")); pheno_catalog_summary(c)
#> # A tibble: 1 × 3
#>   experiments schema_versions root                                              
#>         <int> <chr>           <chr>                                             
#> 1           0 ""              C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/cat_…
pheno_catalog_summary(pheno_catalog(file.path(tempdir(),"cat_sum2")))
#> # A tibble: 1 × 3
#>   experiments schema_versions root                                              
#>         <int> <chr>           <chr>                                             
#> 1           0 ""              C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/cat_…
pheno_catalog_find(c)
#> # A tibble: 0 × 6
#> # ℹ 6 variables: experiment_id <lgl>, study <lgl>, title <lgl>, root <lgl>,
#> #   schema_version <lgl>, updated <lgl>
```
