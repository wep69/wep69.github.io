# Uncertainty, Ensembles, and Classification Stability

## Scope

Version 0.7.0 separates uncertainty propagation from the deterministic
land-suitability model. The package does not infer measurement error or
parameter distributions. The analyst declares uncertainty explicitly and
can then retain Monte Carlo or scenario/model members long enough to
quantify uncertainty in continuous suitability and final classes.

## Declaring uncertainty

``` r

criteria <- matrix(c(
  0.55, 0.80, 0.70,
  0.65, 0.75, 0.60
), nrow=2, byrow=TRUE)
colnames(criteria) <- c("climate","soil","terrain")

u <- uncertainty_spec(
  criteria,
  score_concentration = c(climate=80, soil=120, terrain=100),
  weight_log_sd = c(climate=0.10, soil=0.05, terrain=0.05)
)
```

The Beta concentration controls score dispersion while preserving the
\[0,1\] support. A concentration of `Inf` makes that criterion
deterministic. Weight uncertainty is multiplicative lognormal and never
changes a zero weight into a positive one.

## Monte Carlo propagation

``` r

mc <- suit_monte_carlo(
  criteria, u,
  n=1000, seed=2026,
  method="weighted_arithmetic",
  weights=c(climate=0.4, soil=0.4, terrain=0.2)
)

uncertainty_summary(mc)
class_probability(mc)
class_stability(mc)
```

Raster Monte Carlo remains native to `terra`. The optional Python
backend uses NumPy only for numeric matrix/data-frame workflows,
avoiding expensive raster conversion through `reticulate`.

## Scenario/model ensembles

A deterministic scenario analysis from 0.6.0 can be retained as an
ensemble rather than averaged immediately.

``` r

ens <- ensemble_suitability(scenario_results)
summary <- uncertainty_summary(ens)
prob <- class_probability(ens)
stability <- class_stability(prob)
```

This yields P05/P50/P95 and probabilities such as P(S1), P(S2), P(S3),
and P(N), making disagreement among GCMs or scenarios visible.

## Marginal source decomposition

``` r

design <- data.frame(
  GCM = c("G1","G1","G2","G2"),
  SSP = c("245","585","245","585")
)
parts <- uncertainty_decompose(ens, design, sources=c("GCM","SSP"))
```

The reported shares are marginal eta-squared values. They are
diagnostic, not Sobol indices. When sources are correlated or the design
is non-orthogonal, shares can overlap and must not be added as if they
were an exact variance partition.
