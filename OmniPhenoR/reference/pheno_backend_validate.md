# Validate backend availability without installing software

Validate backend availability without installing software

## Usage

``` r
pheno_backend_validate(backend_id, initialize_python = FALSE, path = NULL)
```

## Arguments

- backend_id:

  Backend identifier.

- initialize_python:

  Allow Python initialization for Python backends.

- path:

  Optional external-backend path, especially LeafMachine2.

## Value

A one-row tibble.

## Examples

``` r
pheno_backend_validate("native")
#> # A tibble: 1 × 6
#>   backend_id available version status      license          note                
#>   <chr>      <lgl>     <chr>   <chr>       <chr>            <chr>               
#> 1 native     TRUE      NA      recommended MIT (OmniPhenoR) Core adapter/format…
pheno_backend_validate("plantcv", initialize_python = FALSE)
#> # A tibble: 1 × 6
#>   backend_id available version status       license note 
#>   <chr>      <lgl>     <chr>   <chr>        <chr>   <chr>
#> 1 plantcv    FALSE     NA      experimental MPL-2.0 ""   
pheno_backend_validate("leafmachine2", path = tempdir())
#> # A tibble: 1 × 6
#>   backend_id   available version status       license note                      
#>   <chr>        <lgl>     <chr>   <chr>        <chr>   <chr>                     
#> 1 leafmachine2 FALSE     NA      experimental GPL-3.0 Set path or option OmniPh…
```
