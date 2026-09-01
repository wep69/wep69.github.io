# Identify the second-ranked crop

Identify the second-ranked crop

## Usage

``` r
second_best_crop(
  x,
  tie_policy = c("NA", "first"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  Multi-crop comparison input.

- tie_policy:

  \`NA\` (default) leaves the winning crop undefined when two or more
  crops share the maximum score; \`first\` uses stable crop order but
  still reports the number tied.

- na_policy:

  Missing-value policy.

## Value

An \`agri_crop_selection\` object for the runner-up crop. When the top
score is tied and \`tie_policy = "NA"\`, the runner-up code remains
undefined.
