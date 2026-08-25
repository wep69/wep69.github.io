# Inspect a BrAPI client without network access

Inspect a BrAPI client without network access

## Usage

``` r
pheno_brapi_status(client)
```

## Arguments

- client:

  `pheno_brapi`.

## Value

One-row tibble.

## Examples

``` r
pheno_brapi_status(pheno_brapi("https://example.org"))
#> # A tibble: 1 × 4
#>   base_url            version has_token httr2_available
#>   <chr>               <chr>   <lgl>     <lgl>          
#> 1 https://example.org 2.1     FALSE     TRUE           
pheno_brapi_status(pheno_brapi("https://example.org",version="2.1"))
#> # A tibble: 1 × 4
#>   base_url            version has_token httr2_available
#>   <chr>               <chr>   <lgl>     <lgl>          
#> 1 https://example.org 2.1     FALSE     TRUE           
pheno_brapi_status(pheno_brapi("https://example.org","secret"))$has_token
#> [1] TRUE
```
