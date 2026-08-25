# List prioritized BrAPI v2 capabilities

List prioritized BrAPI v2 capabilities

## Usage

``` r
pheno_brapi_capabilities(client = NULL)
```

## Arguments

- client:

  Optional client.

## Value

Tibble of modules/entities/endpoints.

## Examples

``` r
pheno_brapi_capabilities()
#> # A tibble: 9 × 4
#>   module      entity               endpoint                   direction 
#>   <chr>       <chr>                <chr>                      <chr>     
#> 1 Core        Studies              /brapi/v2/studies          read/write
#> 2 Core        Locations            /brapi/v2/locations        read/write
#> 3 Germplasm   Germplasm            /brapi/v2/germplasm        read/write
#> 4 Phenotyping ObservationUnits     /brapi/v2/observationunits read/write
#> 5 Phenotyping Observations         /brapi/v2/observations     read/write
#> 6 Phenotyping ObservationVariables /brapi/v2/variables        read/write
#> 7 Phenotyping Traits               /brapi/v2/traits           read/write
#> 8 Phenotyping Methods              /brapi/v2/methods          read/write
#> 9 Phenotyping Scales               /brapi/v2/scales           read/write
subset(pheno_brapi_capabilities(),module=="Phenotyping")
#> # A tibble: 6 × 4
#>   module      entity               endpoint                   direction 
#>   <chr>       <chr>                <chr>                      <chr>     
#> 1 Phenotyping ObservationUnits     /brapi/v2/observationunits read/write
#> 2 Phenotyping Observations         /brapi/v2/observations     read/write
#> 3 Phenotyping ObservationVariables /brapi/v2/variables        read/write
#> 4 Phenotyping Traits               /brapi/v2/traits           read/write
#> 5 Phenotyping Methods              /brapi/v2/methods          read/write
#> 6 Phenotyping Scales               /brapi/v2/scales           read/write
pheno_brapi_capabilities(pheno_brapi("https://example.org"))
#> # A tibble: 9 × 4
#>   module      entity               endpoint                   direction 
#>   <chr>       <chr>                <chr>                      <chr>     
#> 1 Core        Studies              /brapi/v2/studies          read/write
#> 2 Core        Locations            /brapi/v2/locations        read/write
#> 3 Germplasm   Germplasm            /brapi/v2/germplasm        read/write
#> 4 Phenotyping ObservationUnits     /brapi/v2/observationunits read/write
#> 5 Phenotyping Observations         /brapi/v2/observations     read/write
#> 6 Phenotyping ObservationVariables /brapi/v2/variables        read/write
#> 7 Phenotyping Traits               /brapi/v2/traits           read/write
#> 8 Phenotyping Methods              /brapi/v2/methods          read/write
#> 9 Phenotyping Scales               /brapi/v2/scales           read/write
```
