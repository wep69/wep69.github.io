# Fingerprint a set of input files

Fingerprint a set of input files

## Usage

``` r
land_fingerprint_files(paths, root = NULL)
```

## Arguments

- paths:

  File paths (or a folder, whose files are listed recursively).

- root:

  Optional project root; paths inside it are stored relative.

## Value

Data frame with \`file\`, \`bytes\` and \`sha256\`.

## Examples

``` r
d <- file.path(tempdir(), "agri_proj"); dir.create(file.path(d, "data"), recursive = TRUE, showWarnings = FALSE)
f <- file.path(d, "data", "inputs.csv"); utils::write.csv(data.frame(x = 1:3), f, row.names = FALSE)
land_fingerprint_files(file.path(d, "data"), root = d)
#>              file bytes
#> 1 data/inputs.csv    14
#>                                                             sha256
#> 1 52bfc3d759f8dda28a0d1d3ffdea031f64ace7bf1730e18416c0e2a1fd26fcfc
```
