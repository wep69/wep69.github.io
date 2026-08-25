# Publication readiness checklist

Publication readiness checklist

## Usage

``` r
pheno_publish_check(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

List of checks and overall readiness.

## Examples

``` r
pheno_publish_check(pheno_experiment("demo"))
#> $checks
#> # A tibble: 4 × 3
#>   check                            pass  detail                           
#>   <chr>                            <lgl> <chr>                            
#> 1 MIAPPE required metadata         FALSE 7 required fields missing        
#> 2 FAIR license/provenance evidence FALSE 12 FAIR evidence items incomplete
#> 3 Manifest integrity               TRUE  Verify registered files          
#> 4 Privacy review                   TRUE  Review detected privacy items    
#> 
#> $ready
#> [1] FALSE
#> 
e<-pheno_experiment("demo");e$licenses<-pheno_license("cc","CC BY 4.0")
pheno_publish_check(e)$ready
#> [1] FALSE
names(pheno_publish_check(e))
#> [1] "checks" "ready" 
```
