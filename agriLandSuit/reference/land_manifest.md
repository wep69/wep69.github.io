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
  overwrite = FALSE,
  root = NULL
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

- root:

  Optional project root. When supplied, input paths inside \`root\` are
  stored relative to it, so the manifest stays valid when the project
  folder is moved or shared.

## Value

An \`agri_run_manifest\` object, invisibly if written.

## Examples

``` r
d <- file.path(tempdir(), "agri_proj"); dir.create(file.path(d, "data"), recursive = TRUE, showWarnings = FALSE)
f <- file.path(d, "data", "inputs.csv"); utils::write.csv(data.frame(x = 1:3), f, row.names = FALSE)
m <- land_manifest(1:3, inputs = c(inputs = f), root = d, parameters = list(awc = 100))
m$provenance$inputs$path
#> [1] "data/inputs.csv"
```
