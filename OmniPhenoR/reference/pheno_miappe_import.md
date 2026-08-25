# Import a MIAPPE-aligned export

Import a MIAPPE-aligned export

## Usage

``` r
pheno_miappe_import(x)
```

## Arguments

- x:

  Object or path produced by `pheno_miappe_export`.

## Value

Minimal `pheno_experiment` retaining imported MIAPPE fields.

## Examples

``` r
x<-pheno_miappe_export(pheno_experiment("demo","Demo"),format="list");pheno_miappe_import(x)
#> <pheno_experiment> demo 
#>   id: exp_8369695722fb  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
p<-tempfile(fileext=".tsv")
pheno_miappe_export(pheno_experiment("demo"),p,"tsv")
pheno_miappe_import(p)
#> <pheno_experiment> demo 
#>   id: exp_2f4870aa76fc  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
inherits(pheno_miappe_import(x),"pheno_experiment")
#> [1] TRUE
```
