# Build or write an agriLandSuit run manifest

Build or write an agriLandSuit run manifest

## Usage

``` r
land_manifest(
  x = NULL,
  inputs = NULL,
  parameters = list(),
  notes = NULL,
  content = TRUE,
  path = NULL,
  pretty = TRUE,
  overwrite = FALSE
)
```

## Arguments

- x:

  Optional object to fingerprint.

- inputs:

  Optional input file paths to hash.

- parameters:

  Named list of analysis parameters.

- notes:

  Optional free-text note.

- content:

  Include object content in the fingerprint.

- path:

  Optional \`.json\` or \`.rds\` output path.

- pretty:

  Pretty-print JSON when possible.

- overwrite:

  Allow replacement of an existing manifest.

## Value

An \`agri_run_manifest\` object, invisibly if written.
