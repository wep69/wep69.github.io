# Compute a deterministic agriLandSuit fingerprint

Computes a SHA-256 fingerprint from package objects, including raster
geometry and, by default, raster cell content read in blocks. This is
intended for reproducibility checks, not for cryptographic
authentication of third parties.

## Usage

``` r
land_fingerprint(x, content = TRUE)
```

## Arguments

- x:

  Any R object, typically an agriLandSuit object.

- content:

  Include raster/vector content in addition to metadata.

## Value

A SHA-256 character scalar with class \`agri_fingerprint\`.
