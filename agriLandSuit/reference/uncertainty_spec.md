# Define criterion-score and weight uncertainty

Score uncertainty is represented with a Beta distribution centered on
each observed suitability score. \`score_concentration\` is the Beta
concentration (alpha + beta): larger values imply less dispersion and
\`Inf\` makes the criterion deterministic. Weight uncertainty is
multiplicative lognormal, with \`weight_log_sd\` defined on the log
scale. No uncertainty is inferred automatically.

## Usage

``` r
uncertainty_spec(x, score_concentration = Inf, weight_log_sd = 0)
```

## Arguments

- x:

  Criterion-score input accepted by \`suit_aggregate()\`.

- score_concentration:

  Positive scalar or one value per criterion. \`Inf\` means no score
  perturbation.

- weight_log_sd:

  Non-negative scalar or one value per criterion. Zero means no weight
  perturbation.

## Value

An \`agri_uncertainty_spec\` object.
