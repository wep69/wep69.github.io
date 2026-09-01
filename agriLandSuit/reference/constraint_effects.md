# Combine evaluated rules into exclusion, cap, and penalty effects

Multiple caps use the most restrictive (minimum) cap. Multiple penalties
are multiplicative. Any active exclusion marks the cell as excluded.

## Usage

``` r
constraint_effects(x)
```

## Arguments

- x:

  An \`agri_constraint_eval\` object.

## Value

An \`agri_constraint_effects\` object with three aligned SpatRasters:
\`excluded\`, \`cap\`, and \`penalty\`.
