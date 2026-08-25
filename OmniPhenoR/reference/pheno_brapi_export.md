# Map an experiment to BrAPI payloads

Map an experiment to BrAPI payloads

## Usage

``` r
pheno_brapi_export(experiment, dry_run = TRUE)
```

## Arguments

- experiment:

  `pheno_experiment`.

- dry_run:

  Always `TRUE` unless the caller explicitly sends payloads.

## Value

Named list of BrAPI-like payloads.

## Examples

``` r
pheno_brapi_export(pheno_experiment("demo","Demo"))
#> $study
#> $study$studyDbId
#> [1] "demo"
#> 
#> $study$studyName
#> [1] "Demo"
#> 
#> 
#> $locations
#> data frame with 0 columns and 0 rows
#> 
#> $germplasm
#> data frame with 0 columns and 0 rows
#> 
#> $observationVariables
#> data frame with 0 columns and 0 rows
#> 
#> $observations
#> data frame with 0 columns and 0 rows
#> 
#> $dry_run
#> [1] TRUE
#> 
e<-pheno_experiment("demo")
v<-pheno_variable("v","height","ruler","continuous","cm")
e<-pheno_register(e,"variable",v)
names(pheno_brapi_export(e))
#> [1] "study"                "locations"            "germplasm"           
#> [4] "observationVariables" "observations"         "dry_run"             
```
