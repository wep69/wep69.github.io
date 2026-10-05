# Classify numeric suitability scores

Vector classification with an explicit interval convention. \`closed =
"left"\` uses intervals \`\[0, 0.25)\`, \`\[0.25, 0.5)\`, \`\[0.5,
0.75)\`, \`\[0.75, 1\]\`, so a score of exactly 0.5 is S2; \`closed =
"right"\` reproduces \`suit_classify()\` (\`\[0, 0.25\]\`, \`(0.25,
0.5\]\`, ...). State the convention in reports, because it changes the
class of scores that fall exactly on a break.

## Usage

``` r
score_to_class(
  x,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = c("left", "right")
)
```

## Arguments

- x:

  Numeric scores in \[0, 1\].

- breaks:

  Strictly increasing class breaks from 0 to 1.

- labels:

  Class labels from least to most suitable.

- closed:

  \`left\` or \`right\`.

## Value

Factor with levels \`labels\`.

## Examples

``` r
score_to_class(c(0, 0.25, 0.5, 0.75, 1))
#> [1] N  S3 S2 S1 S1
#> Levels: N S3 S2 S1
score_to_class(c(0, 0.25, 0.5, 0.75, 1), closed = "right")
#> [1] N  N  S3 S2 S1
#> Levels: N S3 S2 S1
```
