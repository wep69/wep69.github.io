# Suitability calendar (long table of members)

Suitability calendar (long table of members)

## Usage

``` r
suit_calendar(x, breaks = x$breaks, labels = x$labels, closed = "left")
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\` with matrix draws.

- breaks, labels, closed:

  Class definition for \`score_to_class()\`.

## Value

Long data frame with \`unit\`, \`member\`, the design columns, \`score\`
and \`class\`, ready for a tile plot (\`plot_suitability_calendar()\`).

## Examples

``` r
set.seed(5)
sc <- matrix(runif(30), nrow = 3, dimnames = list(c("A", "B", "C"), NULL))
e <- ensemble_from_scores(sc, design = data.frame(year = factor(1991:2000)))
head(suit_calendar(e))
#>   unit     member year     score class
#> 1    A member_001 1991 0.2002145     N
#> 2    B member_001 1991 0.6852186    S2
#> 3    C member_001 1991 0.9168758    S1
#> 4    A member_002 1992 0.2843995    S3
#> 5    B member_002 1992 0.1046501     N
#> 6    C member_002 1992 0.7010575    S2
```
