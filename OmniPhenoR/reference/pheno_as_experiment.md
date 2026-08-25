# Convert a legacy project to an experiment

Convert a legacy project to an experiment

## Usage

``` r
pheno_as_experiment(x, root = NULL)
```

## Arguments

- x:

  `pheno_project` or `pheno_experiment`.

- root:

  Optional replacement root.

## Value

`pheno_experiment`.

## Examples

``` r
pheno_as_experiment(pheno_project("demo"))
#> <pheno_experiment> demo 
#>   id: exp_327bfe0f6721  schema: 0.5 
#>   tables: 1  files: 0 
#>   variables: 0  germplasm: 0 
pheno_as_experiment(pheno_project("demo", metadata=list(year=2026)))
#> <pheno_experiment> demo 
#>   id: exp_d3f54e448857  schema: 0.5 
#>   tables: 1  files: 0 
#>   variables: 0  germplasm: 0 
pheno_as_experiment(pheno_experiment("already"))
#> <pheno_experiment> already 
#>   id: exp_441ddd6e94f3  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
```
