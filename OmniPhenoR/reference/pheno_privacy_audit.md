# Audit common privacy and publication hazards

Audit common privacy and publication hazards

## Usage

``` r
pheno_privacy_audit(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

Diagnostic tibble.

## Examples

``` r
pheno_privacy_audit(pheno_experiment("demo"))
#> # A tibble: 5 × 3
#>   check                 detected action                                         
#>   <chr>                 <lgl>    <chr>                                          
#> 1 email                 FALSE    Review whether personal contact should be publ…
#> 2 absolute_windows_path FALSE    Replace local absolute paths with relative pat…
#> 3 absolute_unix_path    FALSE    Replace local absolute paths with relative pat…
#> 4 token_keyword         FALSE    Remove credentials from objects/files          
#> 5 precise_coordinates   FALSE    Consider coordinate precision/privacy before p…
e<-pheno_experiment("demo",metadata=list(contact="name@example.org"));pheno_privacy_audit(e)
#> # A tibble: 5 × 3
#>   check                 detected action                                         
#>   <chr>                 <lgl>    <chr>                                          
#> 1 email                 TRUE     Review whether personal contact should be publ…
#> 2 absolute_windows_path FALSE    Replace local absolute paths with relative pat…
#> 3 absolute_unix_path    FALSE    Replace local absolute paths with relative pat…
#> 4 token_keyword         FALSE    Remove credentials from objects/files          
#> 5 precise_coordinates   FALSE    Consider coordinate precision/privacy before p…
e<-pheno_experiment("demo",root="C:/Users/name/project");pheno_privacy_audit(e)
#> # A tibble: 5 × 3
#>   check                 detected action                                         
#>   <chr>                 <lgl>    <chr>                                          
#> 1 email                 FALSE    Review whether personal contact should be publ…
#> 2 absolute_windows_path TRUE     Replace local absolute paths with relative pat…
#> 3 absolute_unix_path    TRUE     Replace local absolute paths with relative pat…
#> 4 token_keyword         FALSE    Remove credentials from objects/files          
#> 5 precise_coordinates   FALSE    Consider coordinate precision/privacy before p…
```
