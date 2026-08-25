# Create or open an experiment catalog

Create or open an experiment catalog

## Usage

``` r
pheno_catalog(root, create = TRUE)
```

## Arguments

- root:

  Catalog directory.

- create:

  Create the directory/index when absent.

## Value

A `pheno_catalog`.

## Examples

``` r
c1 <- pheno_catalog(file.path(tempdir(), "pheno_catalog_demo"))
c2 <- pheno_catalog(file.path(tempdir(), "pheno_catalog_demo"), create=TRUE)
pheno_catalog_summary(c1)
#> # A tibble: 1 × 3
#>   experiments schema_versions root                                              
#>         <int> <chr>           <chr>                                             
#> 1           0 ""              C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/phen…
```
