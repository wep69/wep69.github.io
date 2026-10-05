# Save a figure for publication

Writes a raster version (PNG or TIFF, 600 dpi by default, using \`ragg\`
when available) and a vector PDF (Cairo when available) with the same
physical size. Common journal widths are 90 mm (one column), 140 mm (1.5
columns) and 190 mm (two columns).

## Usage

``` r
save_publication_figure(
  plot,
  file,
  width_mm = 190,
  height_mm = 120,
  dpi = 600,
  formats = c("png", "pdf")
)
```

## Arguments

- plot:

  A \`ggplot\` (or any object printed to a device).

- file:

  Output path without extension (an extension is removed).

- width_mm, height_mm:

  Size in millimetres.

- dpi:

  Resolution of raster outputs.

- formats:

  Any of \`png\`, \`tiff\`, \`pdf\`.

## Value

Invisibly, the paths written.

## Examples

``` r
if (requireNamespace("ggplot2", quietly = TRUE)) {
  p <- plot_membership(crop_profile_library("maize")$maize)
  out <- save_publication_figure(p, file.path(tempdir(), "membership"), width_mm = 140, height_mm = 90, dpi = 150)
  file.exists(out)
}
#> [1] TRUE TRUE
```
