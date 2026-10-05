# Capture provenance for an analysis object or run

Capture provenance for an analysis object or run

## Usage

``` r
land_provenance(
  x = NULL,
  inputs = NULL,
  parameters = list(),
  notes = NULL,
  content = TRUE,
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

- root:

  Optional project root. When supplied, input paths inside \`root\` are
  stored relative to it, so the manifest stays valid when the project
  folder is moved or shared.

## Value

An \`agri_provenance\` object.

## Examples

``` r
d <- file.path(tempdir(), "agri_proj"); dir.create(file.path(d, "data"), recursive = TRUE, showWarnings = FALSE)
f <- file.path(d, "data", "inputs.csv"); utils::write.csv(data.frame(x = 1:3), f, row.names = FALSE)
prov <- land_provenance(1:3, inputs = c(inputs = f), root = d)
prov$inputs
#>     name            path exists
#> 1 inputs data/inputs.csv   TRUE
#>                                                             sha256 bytes
#> 1 52bfc3d759f8dda28a0d1d3ffdea031f64ace7bf1730e18416c0e2a1fd26fcfc    14
```
