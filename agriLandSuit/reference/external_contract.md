# Inspect or validate an external interoperability contract

Inspect or validate an external interoperability contract

## Usage

``` r
external_contract(
  type = c("raster", "land_data", "climate_risk", "suitability"),
  x = NULL
)
```

## Arguments

- type:

  One of \`raster\`, \`land_data\`, \`climate_risk\`, or
  \`suitability\`.

- x:

  Optional object to validate against the selected contract.

## Value

An \`agri_external_contract\` when \`x\` is NULL, otherwise an
\`agri_validation\` object.
