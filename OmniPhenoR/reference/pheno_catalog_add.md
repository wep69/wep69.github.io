# Add an experiment to a catalog

Add an experiment to a catalog

## Usage

``` r
pheno_catalog_add(catalog, experiment, replace = TRUE)
```

## Arguments

- catalog:

  `pheno_catalog`.

- experiment:

  `pheno_experiment`.

- replace:

  Replace an existing experiment id.

## Value

Updated catalog invisibly.

## Examples

``` r
c <- pheno_catalog(file.path(tempdir(),"cat_add")); e<-pheno_experiment("s1",root=tempdir())
pheno_catalog_add(c,e)
pheno_catalog_find(c,study="s1")
#> # A tibble: 1 × 6
#>   experiment_id    study title root                       schema_version updated
#>   <chr>            <chr> <chr> <chr>                               <dbl> <chr>  
#> 1 exp_b58f5187a35e s1    s1    C:/Users/wep69/AppData/Lo…            0.5 2026-0…
pheno_catalog_summary(c)
#> # A tibble: 1 × 3
#>   experiments schema_versions root                                              
#>         <int> <chr>           <chr>                                             
#> 1           1 0.5             C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/cat_…
```
