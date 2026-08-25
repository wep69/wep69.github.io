# Relocate an experiment root without rewriting relative paths

Relocate an experiment root without rewriting relative paths

## Usage

``` r
pheno_relocate(experiment, root)
```

## Arguments

- experiment:

  `pheno_experiment`.

- root:

  New root.

## Value

Updated experiment.

## Examples

``` r
e<-pheno_experiment("demo",root=tempdir());pheno_relocate(e,tempdir())
#> <pheno_experiment> demo 
#>   id: exp_6a0f9ef008d7  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
pheno_relocate(e,file.path(tempdir(),"moved"))$root
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/moved"
inherits(pheno_relocate(e,tempdir()),"pheno_experiment")
#> [1] TRUE
```
