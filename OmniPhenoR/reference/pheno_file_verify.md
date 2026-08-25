# Verify registered files

Verify registered files

## Usage

``` r
pheno_file_verify(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

Manifest verification tibble.

## Examples

``` r
pheno_file_verify(pheno_experiment("demo"))
#> # A tibble: 0 × 3
#> # ℹ 3 variables: file_id <chr>, exists <lgl>, checksum_ok <lgl>
e<-pheno_experiment("demo");nrow(pheno_file_verify(e))
#> [1] 0
names(pheno_file_verify(e))
#> [1] "file_id"     "exists"      "checksum_ok"
```
