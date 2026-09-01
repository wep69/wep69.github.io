# Create a validated set of land scenarios

Create a validated set of land scenarios

## Usage

``` r
scenario_set(..., require_baseline = TRUE, strict_geometry = TRUE)
```

## Arguments

- ...:

  Two or more \`agri_land_scenario\` objects, or one list containing
  them.

- require_baseline:

  Require exactly one baseline scenario.

- strict_geometry:

  Require all scenario templates to share CRS, extent, rows, columns,
  resolution, and origin.

## Value

An \`agri_scenario_set\`.
