# Define a data/model/software license record

Define a data/model/software license record

## Usage

``` r
pheno_license(license_id, name, uri = NA_character_, applies_to = "data")
```

## Arguments

- license_id:

  Identifier.

- name:

  License name.

- uri:

  License URI.

- applies_to:

  Data, image, model, software, or metadata.

## Value

One-row data frame.

## Examples

``` r
pheno_license("ccby4","CC BY 4.0","https://creativecommons.org/licenses/by/4.0/","data")
#>   license_id      name                                          uri applies_to
#> 1      ccby4 CC BY 4.0 https://creativecommons.org/licenses/by/4.0/       data
pheno_license("mit","MIT","https://opensource.org/license/mit","software")
#>   license_id name                                uri applies_to
#> 1        mit  MIT https://opensource.org/license/mit   software
pheno_license("restricted","Restricted use",applies_to="images")
#>   license_id           name  uri applies_to
#> 1 restricted Restricted use <NA>     images
```
