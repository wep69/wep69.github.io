# Aggregate weights or AHP judgments across experts

Aggregate weights or AHP judgments across experts

## Usage

``` r
expert_weights(
  x,
  input = c("auto", "weights", "matrices"),
  method = c("geometric", "arithmetic"),
  ahp_method = "eigen",
  ...
)
```

## Arguments

- x:

  A list of \`agri_ahp\` objects, named numeric weight vectors, or
  complete reciprocal matrices.

- input:

  \`auto\`, \`weights\`, or \`matrices\`.

- method:

  For weight vectors, \`geometric\` or \`arithmetic\`. For matrices,
  \`geometric\` performs aggregation of individual judgments (AIJ).

- ahp_method:

  Method passed to \`ahp_weights()\` after matrix aggregation.

- ...:

  Additional arguments passed to \`ahp_weights()\` for matrix input.

## Value

An \`agri_expert_weights\` object.
