# Identify the limiting criterion

Identify the limiting criterion

## Usage

``` r
limiting_factor(x, na_policy = c("propagate", "available"), tolerance = 1e-12)
```

## Arguments

- x:

  Supported criterion-score input.

- na_policy:

  Missing-data policy, as in \`suit_aggregate()\`.

- tolerance:

  Non-negative tolerance used to count ties around the minimum.

## Value

An \`agri_limiting_factor\` object with minimum score, first limiting
criterion index, tie count, and criterion key.
