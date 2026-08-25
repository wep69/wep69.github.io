# Generate a dataset README

Generate a dataset README

## Usage

``` r
pheno_dataset_readme(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

Character vector of Markdown lines.

## Examples

``` r
pheno_dataset_readme(pheno_experiment("demo","Demo experiment"))
#>  [1] "# Demo experiment"                                                  
#>  [2] ""                                                                   
#>  [3] "Study ID: `demo`"                                                   
#>  [4] "Experiment ID: `exp_e17096cfe5a0`"                                  
#>  [5] "OmniPhenoR schema: `0.5`"                                           
#>  [6] ""                                                                   
#>  [7] "## Contents"                                                        
#>  [8] "- Registered files: 0"                                              
#>  [9] "- Phenotyping variables: 0"                                         
#> [10] "- Germplasm records: 0"                                             
#> [11] ""                                                                   
#> [12] "## Reproducibility"                                                 
#> [13] "See metadata/ and checksum/provenance files in the portable bundle."
cat(pheno_dataset_readme(pheno_experiment("demo")),sep="\n")
#> # demo
#> 
#> Study ID: `demo`
#> Experiment ID: `exp_311726d327a0`
#> OmniPhenoR schema: `0.5`
#> 
#> ## Contents
#> - Registered files: 0
#> - Phenotyping variables: 0
#> - Germplasm records: 0
#> 
#> ## Reproducibility
#> See metadata/ and checksum/provenance files in the portable bundle.
length(pheno_dataset_readme(pheno_experiment("demo")))>3
#> [1] TRUE
```
