# Configure a BrAPI client

Configure a BrAPI client

## Usage

``` r
pheno_brapi(base_url, token = NULL, version = "2.1")
```

## Arguments

- base_url:

  BrAPI server base URL.

- token:

  Optional bearer token; preferably leave `NULL` and manage credentials
  outside package objects.

- version:

  BrAPI major/minor version.

## Value

`pheno_brapi`.

## References

Selby P et al. (2019). BrAPI—an application programming interface for
plant breeding applications. Bioinformatics 35:4147-4155.
doi:10.1093/bioinformatics/btz190. Selby P et al. (2025). BrAPI v2:
real-world applications for data integration and collaboration. Database
2025:baaf048. doi:10.1093/database/baaf048.

## Examples

``` r
pheno_brapi("https://example.org")
#> $base_url
#> [1] "https://example.org"
#> 
#> $token
#> NULL
#> 
#> $version
#> [1] "2.1"
#> 
#> attr(,"class")
#> [1] "pheno_brapi"
pheno_brapi("https://example.org","token",version="2.1")
#> $base_url
#> [1] "https://example.org"
#> 
#> $token
#> [1] "token"
#> 
#> $version
#> [1] "2.1"
#> 
#> attr(,"class")
#> [1] "pheno_brapi"
pheno_brapi_status(pheno_brapi("https://example.org"))
#> # A tibble: 1 × 4
#>   base_url            version has_token httr2_available
#>   <chr>               <chr>   <lgl>     <lgl>          
#> 1 https://example.org 2.1     FALSE     TRUE           
```
