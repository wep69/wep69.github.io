# Registry of canonical unit labels

Returns the conservative unit vocabulary used by agriLandSuit metadata.
Units are stored as explicit metadata labels. The package never performs
hidden physical-unit conversion; the optional \`units\` package may be
used by downstream workflows when conversion is required.

## Usage

``` r
unit_registry()
```

## Value

A data.frame with canonical unit labels and semantic domains.
