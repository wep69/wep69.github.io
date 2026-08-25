# MIAPPE 1.2 core mapping used by OmniPhenoR

The mapping is intentionally a focused interoperability layer rather
than a claim to reproduce every field of the MIAPPE standard. Release
validation should compare this table to the current official MIAPPE 1.2
checklist.

## Usage

``` r
pheno_miappe_mapping()
```

## Value

Tibble of sections, fields, requirement level, and OmniPhenoR source.

## References

Papoutsoglou EA et al. (2020). Enabling reusability of plant phenomic
datasets with MIAPPE 1.1. New Phytologist 227:260-273.
doi:10.1111/nph.16544. Current checklist version: MIAPPE 1.2.

## Examples

``` r
pheno_miappe_mapping()
#> # A tibble: 20 × 4
#>    section             field                     requirement source             
#>    <chr>               <chr>                     <chr>       <chr>              
#>  1 Investigation       Investigation unique ID   required    experiment_id      
#>  2 Investigation       Investigation title       required    title              
#>  3 Investigation       Investigation description recommended metadata.descripti…
#>  4 Study               Study unique ID           required    study              
#>  5 Study               Study start date          recommended metadata.start_date
#>  6 Study               Study end date            recommended metadata.end_date  
#>  7 Study               Study type                recommended metadata.study_type
#>  8 Person              Person name               recommended people.name        
#>  9 Person              Person ORCID              recommended people.orcid       
#> 10 Location            Location name             required    locations.name     
#> 11 Location            Location latitude         recommended locations.latitude 
#> 12 Location            Location longitude        recommended locations.longitude
#> 13 Biological material Biological material ID    required    germplasm.germplas…
#> 14 Biological material Genus species             required    germplasm.species  
#> 15 Observed variable   Variable ID               required    variables.variable…
#> 16 Observed variable   Trait                     required    variables.trait    
#> 17 Observed variable   Method                    required    variables.method   
#> 18 Observed variable   Scale                     required    variables.scale    
#> 19 Observed variable   Unit                      recommended variables.unit     
#> 20 Data file           Data file link            recommended files.path         
subset(pheno_miappe_mapping(), requirement=="required")
#> # A tibble: 10 × 4
#>    section             field                   requirement source               
#>    <chr>               <chr>                   <chr>       <chr>                
#>  1 Investigation       Investigation unique ID required    experiment_id        
#>  2 Investigation       Investigation title     required    title                
#>  3 Study               Study unique ID         required    study                
#>  4 Location            Location name           required    locations.name       
#>  5 Biological material Biological material ID  required    germplasm.germplasm_…
#>  6 Biological material Genus species           required    germplasm.species    
#>  7 Observed variable   Variable ID             required    variables.variable_id
#>  8 Observed variable   Trait                   required    variables.trait      
#>  9 Observed variable   Method                  required    variables.method     
#> 10 Observed variable   Scale                   required    variables.scale      
subset(pheno_miappe_mapping(), section=="Observed variable")
#> # A tibble: 5 × 4
#>   section           field       requirement source               
#>   <chr>             <chr>       <chr>       <chr>                
#> 1 Observed variable Variable ID required    variables.variable_id
#> 2 Observed variable Trait       required    variables.trait      
#> 3 Observed variable Method      required    variables.method     
#> 4 Observed variable Scale       required    variables.scale      
#> 5 Observed variable Unit        recommended variables.unit       
```
