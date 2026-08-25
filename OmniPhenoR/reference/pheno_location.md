# Define an experimental location

Define an experimental location

## Usage

``` r
pheno_location(
  location_id,
  name,
  latitude = NA_real_,
  longitude = NA_real_,
  elevation = NA_real_,
  environment = NA_character_
)
```

## Arguments

- location_id:

  Identifier.

- name:

  Name.

- latitude, longitude:

  Coordinates.

- elevation:

  Elevation in m.

- environment:

  Field, greenhouse, chamber, etc.

## Value

One-row data frame.

## Examples

``` r
pheno_location("loc1","Field A",-6.96,-35.69,623,"field")
#>   location_id    name latitude longitude elevation environment
#> 1        loc1 Field A    -6.96    -35.69       623       field
pheno_location("loc2","Greenhouse",environment="greenhouse")
#>   location_id       name latitude longitude elevation environment
#> 1        loc2 Greenhouse       NA        NA        NA  greenhouse
pheno_location("loc3","Growth chamber",environment="growth_chamber")
#>   location_id           name latitude longitude elevation    environment
#> 1        loc3 Growth chamber       NA        NA        NA growth_chamber
```
