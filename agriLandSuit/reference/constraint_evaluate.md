# Evaluate explicit constraints against land layers

Missing source values remain unknown (\`NA\`) rather than being silently
treated as unconstrained.

## Usage

``` r
constraint_evaluate(land, constraints, strict_units = TRUE)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- constraints:

  An \`agri_land_constraint\`, \`agri_constraint_set\`, or list of
  constraint objects.

- strict_units:

  If TRUE, a rule with declared \`unit\` must match source layer unit
  metadata exactly.

## Value

An \`agri_constraint_eval\` object containing one active 0/1 raster per
rule.
