# List supported repeated-measures correlation structures

List supported repeated-measures correlation structures

## Usage

``` r
pheno_correlation_structures()
```

## Value

A tibble describing native names and `nlme` equivalents.

## Examples

``` r
pheno_correlation_structures()
#> # A tibble: 6 × 4
#>   name              engine  nlme_class  description                          
#>   <chr>             <chr>   <chr>       <chr>                                
#> 1 independence      lm/nlme NA          no residual correlation              
#> 2 compound_symmetry nlme    corCompSymm constant within-subject correlation  
#> 3 ar1               nlme    corAR1      discrete AR(1)                       
#> 4 car1              nlme    corCAR1     continuous AR(1)                     
#> 5 gaussian          nlme    corGaus     Gaussian spatial/temporal correlation
#> 6 exponential       nlme    corExp      exponential correlation              
subset(pheno_correlation_structures(), engine == "nlme")
#> # A tibble: 5 × 4
#>   name              engine nlme_class  description                          
#>   <chr>             <chr>  <chr>       <chr>                                
#> 1 compound_symmetry nlme   corCompSymm constant within-subject correlation  
#> 2 ar1               nlme   corAR1      discrete AR(1)                       
#> 3 car1              nlme   corCAR1     continuous AR(1)                     
#> 4 gaussian          nlme   corGaus     Gaussian spatial/temporal correlation
#> 5 exponential       nlme   corExp      exponential correlation              
pheno_correlation_structures()[, c("name","description")]
#> # A tibble: 6 × 2
#>   name              description                          
#>   <chr>             <chr>                                
#> 1 independence      no residual correlation              
#> 2 compound_symmetry constant within-subject correlation  
#> 3 ar1               discrete AR(1)                       
#> 4 car1              continuous AR(1)                     
#> 5 gaussian          Gaussian spatial/temporal correlation
#> 6 exponential       exponential correlation              
```
