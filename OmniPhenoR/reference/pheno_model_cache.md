# Get or create the model-cache directory

Get or create the model-cache directory

## Usage

``` r
pheno_model_cache(path = NULL, create = FALSE)
```

## Arguments

- path:

  Optional cache path.

- create:

  Create the directory explicitly.

## Value

Normalized path.

## Examples

``` r
pheno_model_cache(create=FALSE)
#> [1] "C:/Users/wep69/AppData/Local/R/cache/R/OmniPhenoR/models"
d<-file.path(tempdir(),"omniphenor-models")
pheno_model_cache(d,create=TRUE);unlink(d,recursive=TRUE)
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/omniphenor-models"
pheno_model_cache(file.path(tempdir(), "read-only-example"), create = FALSE)
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/read-only-example"
```
