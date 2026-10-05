# Write a lightweight scientific suitability report

Write a lightweight scientific suitability report

## Usage

``` r
suit_report(x, path, title = NULL, manifest = NULL, overwrite = FALSE)
```

## Arguments

- x:

  An agriLandSuit object with a \`summary()\` method.

- path:

  Output \`.md\` file.

- title:

  Optional report title.

- manifest:

  Optional manifest to summarize.

- overwrite:

  Allow replacement.

## Value

Output path invisibly.

## Examples

``` r
s <- suit_aggregate(cbind(rain = c(0.9, 0.6, 0.3, 0.1), temp = c(1, 0.7, 0.8, 0.5)))
f <- tempfile(fileext = ".md")
suit_report(s, f, title = "Demonstration report")
head(readLines(f), 8)
#> [1] "# Demonstration report"             ""                                  
#> [3] "Generated: 2026-10-05 03:40:53 UTC" ""                                  
#> [5] "Object class: `agri_suitability`"   ""                                  
#> [7] "## Summary"                         ""                                  
```
