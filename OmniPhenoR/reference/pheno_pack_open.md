# Open a portable phenotyping bundle

Open a portable phenotyping bundle

## Usage

``` r
pheno_pack_open(path)
```

## Arguments

- path:

  Bundle directory produced by `pheno_pack`.

## Value

`pheno_experiment`.

## Examples

``` r
p<-file.path(tempdir(),"pack_open_demo")
pheno_pack(pheno_experiment("demo"),p,overwrite=TRUE)
pheno_pack_open(p)
#> <pheno_experiment> demo 
#>   id: exp_39c2126bb05f  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
inherits(pheno_pack_open(p),"pheno_experiment")
#> [1] TRUE
pheno_pack_open(p)$study
#> [1] "demo"
```
