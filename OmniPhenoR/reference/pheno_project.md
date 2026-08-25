# Create an OmniPhenoR project

Creates the central provenance object used to keep study, acquisition,
experimental, spatial, and processing identifiers together.

## Usage

``` r
pheno_project(study, metadata = list(), design = NULL, root = NULL)
```

## Arguments

- study:

  Short study identifier.

- metadata:

  Named list with project metadata.

- design:

  Optional data frame describing plots, plants, or treatments.

- root:

  Optional project root directory.

## Value

An object of class `pheno_project`.

## Examples

``` r
pheno_project("soybean_drought")
#> <pheno_project> soybean_drought 
#>   design rows: 0 
#>   metadata: 0 fields
#>   results: 0 objects
pheno_project("maize_nitrogen", metadata = list(sensor = "RGB", year = 2026))
#> <pheno_project> maize_nitrogen 
#>   design rows: 0 
#>   metadata: 2 fields
#>   results: 0 objects
pheno_project("bean_disease", design = data.frame(plot = 1:3, treatment = c("A","B","C")))
#> <pheno_project> bean_disease 
#>   design rows: 3 
#>   metadata: 0 fields
#>   results: 0 objects
```
