# Combine land constraints into a versionable set

Combine land constraints into a versionable set

## Usage

``` r
constraint_set(..., id = "default", metadata = list(), validate = TRUE)
```

## Arguments

- ...:

  \`agri_land_constraint\` objects or one list of them.

- id:

  Stable set identifier.

- metadata:

  Optional named list.

- validate:

  Validate immediately.

## Value

An \`agri_constraint_set\` object.

## Examples

``` r
rules <- constraint_set(
  land_constraint("steep", "terrain.slope", "exclude", "gt", threshold = 20, unit = "degree"),
  land_constraint("acid", "soil.pH", "cap", "lt", threshold = 5.5, cap = 0.5, unit = "pH"))
rules
#> <agri_constraint_set> default 
#>  rules: 2 
#>  names: steep, acid 
```
