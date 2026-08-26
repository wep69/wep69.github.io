# Extract group-level posterior effects from a brms model

Extract group-level posterior effects from a brms model

## Usage

``` r
bayes_random_effects(fit, group = NULL, level = 0.95)
```

## Arguments

- fit:

  A brms-backed \`bayes_fit\`.

- group:

  Optional grouping-factor name.

- level:

  Credible interval mass.

## Value

The corresponding \`brms::ranef()\` summary.

## Examples

``` r
# Example 1: all grouping factors
if (FALSE) bayes_random_effects(mixed_fit)
# Example 2: one grouping factor
if (FALSE) bayes_random_effects(mixed_fit, "block")
# Example 3: 90 percent intervals
if (FALSE) bayes_random_effects(mixed_fit, level = 0.90)
```
