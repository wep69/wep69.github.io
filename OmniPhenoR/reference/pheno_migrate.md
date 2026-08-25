# Migrate experiment schema metadata

This function performs conservative metadata-level migrations only.
Future releases can add explicit data transformations by source/target
version.

## Usage

``` r
pheno_migrate(experiment, to = "0.5")
```

## Arguments

- experiment:

  `pheno_experiment`.

- to:

  Target schema version.

## Value

Updated experiment.

## Examples

``` r
pheno_migrate(pheno_experiment("demo"),"0.5")
#> <pheno_experiment> demo 
#>   id: exp_dd031a27f16a  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
pheno_migrate(pheno_experiment("demo"),"0.6")$schema_version
#> [1] "0.6"
inherits(pheno_migrate(pheno_experiment("demo"),"0.5"),"pheno_experiment")
#> [1] TRUE
```
