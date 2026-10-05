# Validate a land constraint or constraint set

Validate a land constraint or constraint set

## Usage

``` r
constraint_validate(x)
```

## Arguments

- x:

  An \`agri_land_constraint\` or \`agri_constraint_set\`.

## Value

An \`agri_validation\` object.

## Examples

``` r
rules <- constraint_set(
  land_constraint("steep", "terrain.slope", "exclude", "gt", threshold = 20, unit = "degree"),
  land_constraint("acid", "soil.pH", "cap", "lt", threshold = 5.5, cap = 0.5, unit = "pH"))
constraint_validate(rules)
#> <agri_validation> OK
#> No issues detected.
```
