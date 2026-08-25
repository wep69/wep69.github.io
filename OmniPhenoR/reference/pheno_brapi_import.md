# Import a frozen BrAPI fixture or response

Import a frozen BrAPI fixture or response

## Usage

``` r
pheno_brapi_import(x)
```

## Arguments

- x:

  BrAPI-like list.

## Value

`pheno_experiment`.

## Examples

``` r
fx<-list(study=list(studyDbId="S1",studyName="Trial"),
  germplasm=data.frame(germplasmDbId="G1",germplasmName="Line"))
pheno_brapi_import(fx)
#> <pheno_experiment> S1 
#>   id: exp_d41b2346eb6f  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 1 
inherits(pheno_brapi_import(fx),"pheno_experiment")
#> [1] TRUE
pheno_brapi_import(list(study=list(studyDbId="S2")))$study
#> [1] "S2"
```
