# Apply explicit land constraints to one suitability score

The operation order is cap, then multiplicative penalty, then exclusion.
Unknown constraint states propagate as \`NA\`.

## Usage

``` r
apply_constraints(x, effects, excluded_value = NA_real_)
```

## Arguments

- x:

  A single-layer \`terra::SpatRaster\` suitability score or an
  \`agri_criterion_suit\` object.

- effects:

  An \`agri_constraint_effects\` object.

- excluded_value:

  Value assigned to excluded cells. The default \`NA\` preserves
  exclusion as distinct from agronomic unsuitability.

## Value

A constrained SpatRaster or updated \`agri_criterion_suit\` object.
