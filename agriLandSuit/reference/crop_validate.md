# Validate a crop profile

Validate a crop profile

## Usage

``` r
crop_validate(x, require_sources = FALSE)
```

## Arguments

- x:

  An \`agri_crop_profile\`.

- require_sources:

  Require source and source_id for each agronomic requirement.

## Value

An \`agri_validation\` object.

## Examples

``` r
crop <- crop_profile("demo_maize", "Zea mays", common_name = "maize", requirements = list(
  crop_requirement("Pseason", "climate", "mm", "range", c(400, 600, 1200, 1800), source = "illustrative", source_id = "demo-rain"),
  crop_requirement("LGP", "water", "day", "increasing", c(90, 150), source = "illustrative", source_id = "demo-lgp"),
  crop_requirement("pH", "soil", "pH", "range", c(4.5, 5.5, 7.5, 8.5), source = "illustrative", source_id = "demo-ph"),
  crop_requirement("slope", "terrain", "degree", "decreasing", c(5, 20), source = "illustrative", source_id = "demo-slope")))
crop_validate(crop, require_sources = TRUE)
#> <agri_validation> OK
#> No issues detected.
```
