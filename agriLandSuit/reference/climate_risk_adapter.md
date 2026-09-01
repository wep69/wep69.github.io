# Adapt precomputed climate-risk or climate-layer outputs to agriLandSuit

This adapter deliberately does not calculate climatic risk and does not
automatically invert a risk score into suitability. It only transfers
already-computed raster layers into the agriLandSuit data contract. For
an external package object, provide an \`extractor\` that returns a
\`SpatRaster\`, raster path, named list accepted by \`land_data()\`, or
an \`agri_land_data\`.

## Usage

``` r
climate_risk_adapter(
  x,
  extractor = NULL,
  role = c("climate_input", "constraint_source"),
  units = NULL,
  template = NULL,
  metadata = list(),
  validate = TRUE
)
```

## Arguments

- x:

  External object, \`SpatRaster\`, raster path/list, or
  \`agri_land_data\`.

- extractor:

  Optional function called as \`extractor(x)\`. This is the recommended
  decoupled contract for \`agriClimateRisk\` objects.

- role:

  Store extracted layers as \`climate_input\` or \`constraint_source\`.

- units:

  Optional named unit vector passed to \`land_data()\`.

- template:

  Optional raster template passed to \`land_data()\`.

- metadata:

  Additional metadata. Adapter provenance is added explicitly.

- validate:

  Passed to \`land_data()\`.

## Value

An \`agri_land_data\` object.
