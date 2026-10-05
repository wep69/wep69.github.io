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

## Examples

``` r
land_fingerprint(data.frame(x = 1:3))
#> <agri_fingerprint> b24e4d383ba2edb4e4c7b5bd76f0053f20cfaec11ce8756639bd1f01963f3a97 
land_fingerprint(crop_profile_library("maize")$maize)
#> <agri_fingerprint> 39df7c180af6e81ab28cf5325fe9ed0f06063bda6372f14c2c76e861304f4a6a 
```
