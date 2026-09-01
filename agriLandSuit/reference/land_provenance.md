# Capture provenance for an analysis object or run

Capture provenance for an analysis object or run

## Usage

``` r
land_provenance(
  x = NULL,
  inputs = NULL,
  parameters = list(),
  notes = NULL,
  content = TRUE
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

## Value

An \`agri_provenance\` object.
