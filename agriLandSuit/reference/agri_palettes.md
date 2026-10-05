# Colour palettes and a compact theme for figures

\`agri_palettes()\` returns colour-blind safe palettes: suitability
classes (brown to teal), crops (Okabe-Ito) and a diverging palette for
changes. \`theme_agri()\` is a compact \`ggplot2\` theme sized for
journal columns.

## Usage

``` r
agri_palettes()

theme_agri(base = 8)
```

## Arguments

- base:

  Base font size (points).

## Value

\`agri_palettes()\` a named list of colour vectors; \`theme_agri()\` a
\`ggplot2\` theme.

## Examples

``` r
agri_palettes()$class
#>         N        S3        S2        S1 
#> "#8C510A" "#D8B365" "#5AB4AC" "#01665E" 
if (requireNamespace("ggplot2", quietly = TRUE)) {
  ggplot2::ggplot(data.frame(x = 1:3, y = c(2, 1, 3)), ggplot2::aes(x, y)) + ggplot2::geom_line() + theme_agri()
}
```
