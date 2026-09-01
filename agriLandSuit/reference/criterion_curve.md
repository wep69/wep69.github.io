# Generate a diagnostic membership curve for a crop requirement

Generate a diagnostic membership curve for a crop requirement

## Usage

``` r
criterion_curve(requirement, n = 201L, extend = 0.1)
```

## Arguments

- requirement:

  An \`agri_crop_requirement\`.

- n:

  Number of evaluation points for continuous requirements.

- extend:

  Fraction by which to extend continuous limits on both sides.

## Value

A data.frame with raw criterion values and suitability scores.
