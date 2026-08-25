# Diagnostic FAIR audit

This is an evidence checklist, not a FAIR certification or universal
score.

## Usage

``` r
pheno_fair_audit(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

Tibble of FAIR principle, evidence, status, and recommendation.

## References

Wilkinson MD et al. (2016). The FAIR Guiding Principles for scientific
data management and stewardship. Scientific Data 3:160018.
doi:10.1038/sdata.2016.18.

## Examples

``` r
pheno_fair_audit(pheno_experiment("demo"))
#> # A tibble: 13 × 4
#>    principle evidence status     recommendation                                 
#>    <chr>     <lgl>    <chr>      <chr>                                          
#>  1 F1        FALSE    incomplete Assign a persistent identifier                 
#>  2 F2        FALSE    incomplete Add rich study metadata                        
#>  3 F3        FALSE    incomplete Link metadata to registered files              
#>  4 F4        FALSE    incomplete Register/index dataset in a repository         
#>  5 A1        FALSE    incomplete Document retrieval/access protocol             
#>  6 A1.2      FALSE    incomplete Document authentication/authorization if needed
#>  7 I1        FALSE    incomplete Use machine-readable structured metadata       
#>  8 I2        FALSE    incomplete Use resolvable controlled vocabulary identifie…
#>  9 I3        FALSE    incomplete Record qualified relations/lineage             
#> 10 R1        FALSE    incomplete Provide sufficient domain attributes           
#> 11 R1.1      FALSE    incomplete Declare explicit data usage license            
#> 12 R1.2      TRUE     pass       Record detailed provenance                     
#> 13 R1.3      FALSE    incomplete Use community standards such as MIAPPE/BrAPI w…
e<-pheno_experiment("demo");e$metadata$doi<-"10.x/demo";pheno_fair_audit(e)
#> # A tibble: 13 × 4
#>    principle evidence status     recommendation                                 
#>    <chr>     <lgl>    <chr>      <chr>                                          
#>  1 F1        TRUE     pass       Assign a persistent identifier                 
#>  2 F2        FALSE    incomplete Add rich study metadata                        
#>  3 F3        FALSE    incomplete Link metadata to registered files              
#>  4 F4        FALSE    incomplete Register/index dataset in a repository         
#>  5 A1        FALSE    incomplete Document retrieval/access protocol             
#>  6 A1.2      FALSE    incomplete Document authentication/authorization if needed
#>  7 I1        FALSE    incomplete Use machine-readable structured metadata       
#>  8 I2        FALSE    incomplete Use resolvable controlled vocabulary identifie…
#>  9 I3        FALSE    incomplete Record qualified relations/lineage             
#> 10 R1        FALSE    incomplete Provide sufficient domain attributes           
#> 11 R1.1      FALSE    incomplete Declare explicit data usage license            
#> 12 R1.2      TRUE     pass       Record detailed provenance                     
#> 13 R1.3      FALSE    incomplete Use community standards such as MIAPPE/BrAPI w…
subset(pheno_fair_audit(e),status!="pass")
#> # A tibble: 11 × 4
#>    principle evidence status     recommendation                                 
#>    <chr>     <lgl>    <chr>      <chr>                                          
#>  1 F2        FALSE    incomplete Add rich study metadata                        
#>  2 F3        FALSE    incomplete Link metadata to registered files              
#>  3 F4        FALSE    incomplete Register/index dataset in a repository         
#>  4 A1        FALSE    incomplete Document retrieval/access protocol             
#>  5 A1.2      FALSE    incomplete Document authentication/authorization if needed
#>  6 I1        FALSE    incomplete Use machine-readable structured metadata       
#>  7 I2        FALSE    incomplete Use resolvable controlled vocabulary identifie…
#>  8 I3        FALSE    incomplete Record qualified relations/lineage             
#>  9 R1        FALSE    incomplete Provide sufficient domain attributes           
#> 10 R1.1      FALSE    incomplete Declare explicit data usage license            
#> 11 R1.3      FALSE    incomplete Use community standards such as MIAPPE/BrAPI w…
```
