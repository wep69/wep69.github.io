# Inspect registered files

Inspect registered files

## Usage

``` r
pheno_file_info(experiment, type = NULL)
```

## Arguments

- experiment:

  `pheno_experiment`.

- type:

  Optional file-type filter.

## Value

Tibble.

## Examples

``` r
e<-pheno_experiment("demo");pheno_file_info(e)
#> # A tibble: 0 × 0
pheno_file_info(e,type="image")
#> # A tibble: 0 × 0
nrow(pheno_file_info(e))
#> [1] 0
```
