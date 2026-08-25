# Find experiments in a catalog

Find experiments in a catalog

## Usage

``` r
pheno_catalog_find(catalog, study = NULL, title = NULL)
```

## Arguments

- catalog:

  `pheno_catalog`.

- study, title:

  Optional regular-expression filters.

## Value

Tibble.

## Examples

``` r
c<-pheno_catalog(file.path(tempdir(),"cat_find")); pheno_catalog_find(c)
#> # A tibble: 0 × 6
#> # ℹ 6 variables: experiment_id <lgl>, study <lgl>, title <lgl>, root <lgl>,
#> #   schema_version <lgl>, updated <lgl>
pheno_catalog_find(c,study="soy")
#> # A tibble: 0 × 6
#> # ℹ 6 variables: experiment_id <lgl>, study <lgl>, title <lgl>, root <lgl>,
#> #   schema_version <lgl>, updated <lgl>
pheno_catalog_find(c,title="drought")
#> # A tibble: 0 × 6
#> # ℹ 6 variables: experiment_id <lgl>, study <lgl>, title <lgl>, root <lgl>,
#> #   schema_version <lgl>, updated <lgl>
```
