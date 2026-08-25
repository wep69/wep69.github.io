# Create a file-integrity manifest

Create a file-integrity manifest

## Usage

``` r
pheno_manifest(experiment, output = NULL, refresh = FALSE)
```

## Arguments

- experiment:

  `pheno_experiment`.

- output:

  Optional CSV file.

- refresh:

  Recalculate checksums from current files.

## Value

Manifest tibble.

## Examples

``` r
e<-pheno_experiment("demo");pheno_manifest(e)
#> # A tibble: 0 × 0
out<-tempfile(fileext=".csv");pheno_manifest(e,out);file.exists(out)
#> # A tibble: 0 × 0
#> [1] TRUE
pheno_manifest(e,refresh=FALSE)
#> # A tibble: 0 × 0
```
