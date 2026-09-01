# Export an agriLandSuit object reproducibly

Export an agriLandSuit object reproducibly

## Usage

``` r
land_export(
  x,
  path,
  format = c("bundle", "rds"),
  overwrite = FALSE,
  manifest = TRUE
)
```

## Arguments

- x:

  Object to export.

- path:

  Destination directory for \`bundle\` or file for \`rds\`.

- format:

  \`bundle\` writes spatial payloads as standard geospatial files plus a
  packed object; \`rds\` embeds spatial objects using \`terra::wrap()\`.

- overwrite:

  Allow replacement.

- manifest:

  Write a run manifest alongside the exported object.

## Value

Normalized output path invisibly.
