# Verify an experiment manifest

Verify an experiment manifest

## Usage

``` r
pheno_verify_manifest(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

Tibble with existence and checksum status.

## Examples

``` r
e<-pheno_experiment("demo");pheno_verify_manifest(e)
#> # A tibble: 0 × 3
#> # ℹ 3 variables: file_id <chr>, exists <lgl>, checksum_ok <lgl>
f<-tempfile();writeLines("x",f)
e<-pheno_file_register(pheno_experiment("d",root=tempdir()),f)
pheno_verify_manifest(e)
#> # A tibble: 1 × 6
#>   file_id           path     exists checksum_ok current_sha256 registered_sha256
#>   <chr>             <chr>    <lgl>  <lgl>       <chr>          <chr>            
#> 1 file_2e43544f2b4e file38f… TRUE   TRUE        b35e09fa2ced9… b35e09fa2ced9ebc…
all(c("exists","checksum_ok")%in%names(pheno_verify_manifest(e)))
#> [1] TRUE
```
