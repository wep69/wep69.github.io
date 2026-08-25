# Remove an experiment from a catalog

Remove an experiment from a catalog

## Usage

``` r
pheno_catalog_remove(catalog, experiment_id)
```

## Arguments

- catalog:

  `pheno_catalog`.

- experiment_id:

  Experiment id.

## Value

Catalog invisibly.

## Examples

``` r
c<-pheno_catalog(file.path(tempdir(),"cat_remove"));pheno_catalog_remove(c,"missing")
pheno_catalog_find(c)
#> # A tibble: 0 × 6
#> # ℹ 6 variables: experiment_id <lgl>, study <lgl>, title <lgl>, root <lgl>,
#> #   schema_version <lgl>, updated <lgl>
pheno_catalog_summary(c)
#> # A tibble: 1 × 3
#>   experiments schema_versions root                                              
#>         <int> <chr>           <chr>                                             
#> 1           0 ""              C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/cat_…
```
