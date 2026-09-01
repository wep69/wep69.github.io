# Construct a complete reciprocal AHP matrix

Construct a complete reciprocal AHP matrix

## Usage

``` r
ahp_matrix(criteria, comparisons)
```

## Arguments

- criteria:

  Unique criterion names.

- comparisons:

  Data frame with columns \`criterion1\`, \`criterion2\`, and \`value\`.
  Values express how many times criterion1 is preferred to criterion2.

## Value

An \`agri_ahp_matrix\` object.
