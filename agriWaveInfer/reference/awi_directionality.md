# Summarize phase direction and predictive precedence without causal labeling

Separates phase-directional association from band-limited predictive
precedence and reports whether the two are concordant.

## Usage

``` r
awi_directionality(x, period_range, time_range = NULL, phase_spec = awi_phase_spec())
awi_predictive_direction(
  x, period_range, time_range = NULL, phase_spec = awi_phase_spec(),
  predictive_test = TRUE, lag_order = 1L,
  detrend = c("linear", "mean", "none"), alpha = 0.05
)
```

## Arguments

- x:

  A coherence result retaining phase and, for predictive testing, both
  original input series.

- period_range:

  Two-element period band.

- time_range:

  Optional time window for phase summarization.

- phase_spec:

  Phase interpretation policy.

- predictive_test:

  Whether to add the experimental band-limited Granger-style diagnostic.

- lag_order:

  Positive autoregressive lag order used by the predictive diagnostic.

- detrend:

  Detrending applied before deterministic band filtering.

- alpha:

  Nominal threshold used to classify directional predictive precedence.

## Value

An `awi_directionality` or `awi_predictive_direction` object.

## Details

Neither output is interpreted as an identified causal effect.
Concordance means only that the principal phase direction and the
selected predictive-precedence diagnostic point in the same direction
under the chosen band, lag order, filter, and model.
