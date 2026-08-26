# bayesAgriFlow 1.0.0

`bayesAgriFlow` provides a unified, design-aware Bayesian workflow for
agricultural and biological experiments. Version 1.0.0 is the
consolidation release for the complete 0.1.0–0.6.0 development series.
Version 0.6.0 is the final pre-1.0 development release. There is no
intervening 0.7.0 development release: the functionality planned and
implemented through 0.6.0 is now frozen as the initial stable 1.x API.

## Core principle

The package separates four questions that are often confused in applied
Bayesian work:

1.  **Estimation:** What effect sizes and predictions are plausible
    after observing the data?
2.  **Evidence:** How do specified models or hypotheses compare under
    stated priors?
3.  **Prediction:** How well do models predict new or held-out
    observations?
4.  **Decision:** Which action maximizes expected utility under the
    posterior uncertainty that actually matters for the decision?

The frequentist ANOVA/model counterpart is available only as a
pedagogical crosswalk. It is not used as a gate that validates or
invalidates a Bayesian result.

## Stable 1.0 workflow

``` r

library(bayesAgriFlow)

design <- bayes_rcbd(
  data = bayes_teaching_data("rcbd"),
  response = "yield",
  treatment = "cultivar",
  block = "block"
)

fit <- bayes_fit(design, engine = "native", draws = 4000, seed = 20260825)

bayes_summary(fit)
bayes_contrasts(fit, factor = "cultivar")
bayes_rope(fit, range = c(-0.20, 0.20), factor = "cultivar")
bayes_pp_check(fit)
bayes_report_check(fit)
```

For complex hierarchical models, retain the same design-first logic
while using the optional Stan path:

``` r

compute <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4
)

fit <- bayes_fit(design, engine = "brms", compute = compute)
```

## Consolidated scientific scope

### Experimental designs

- completely randomized designs (CRD/DIC);
- randomized complete block designs (RCBD/DBC);
- Latin squares (DQL);
- factorial experiments;
- split-plot, split-split-plot, and strip-plot structures;
- mixed models, random intercepts and slopes, nested and crossed
  effects;
- repeated measures and selected AR(1) residual structures;
- heterogeneous residual variance for supported continuous models.

### Quantitative models and optima

- linear, quadratic, and cubic polynomial regression;
- ANCOVA;
- qualitative-by-quantitative models;
- posterior biological and economic optima over explicit domains;
- nonlinear Mitscherlich, Michaelis-Menten, four-parameter logistic, and
  Gompertz templates;
- Bayesian splines and Gaussian processes;
- Bayesian bootstrap and optional BART.

### Generalized and complex systems

- Bernoulli, binomial, Poisson, negative-binomial, Gamma, lognormal,
  Beta, and selected ordinal families;
- spatial field trials;
- genotype-by-environment hierarchy;
- multivariate responses;
- predictor measurement error;
- joint continuous missing-data models;
- left, right, and interval censoring;
- ordinal outcomes;
- finite mixtures;
- distributional regression.

### Evidence, prediction, selection, and decision

- Bayes factors and inclusion evidence where mathematically supported;
- posterior treatment contrasts, direction probabilities, ROPE,
  equivalence, and meaningful-effect probabilities;
- posterior predictive checks and MCMC diagnostics;
- PSIS-LOO model comparison;
- stacking and pseudo-BMA predictive weights;
- marginal-likelihood Bayesian model averaging;
- projection predictive selection through optional `projpred`;
- utility, expected regret, probability of optimal action, EVPI, and
  generic EVSI aggregation;
- treatment-specific decision and economic sensitivity workflows.

## Optional backends

The base package does not silently install heavy software.
`BayesFactor`, `brms`, `cmdstanr`, `rstan`, `bridgesampling`, `loo`,
`projpred`, `BFpack`, `PyMC`, and `PyMC-BART` are optional capability
providers. Use
[`bayes_capabilities()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_capabilities.md)
and
[`bayes_compute_info()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compute_info.md)
to inspect the current environment.

## Teaching and reporting

Version 1.0.0 contains 30 extensive English vignettes covering
foundations through advanced experimental systems and Bayesian decision
analysis. The package follows the reporting logic emphasized by BARG and
JASP-oriented Bayesian guidance: specify the scientific goal, model,
likelihood, priors, diagnostics, posterior summaries, sensitivity
analyses, evidence metrics when relevant, and reproducibility details.

## Stability policy

The exported function names, principal S3 classes, and main design -\>
fit -\> summarize/diagnose/predict/report workflow form the initial
stable 1.x API. During the 1.x series, incompatible changes should use a
documented deprecation period whenever feasible. Optional backend
behavior may evolve with upstream packages, but the package will
continue to expose backend provenance and will not silently substitute a
scientifically different model.

## Release validation status

The source snapshot can be statically validated and frozen in this
environment. R, Rscript, CmdStan, brms sampling, vignette rendering,
`R CMD build`, and `R CMD check --as-cran` require a local R environment
and remain mandatory runtime release gates before claiming a CRAN-ready
release. See `inst/dev/LOCAL_VALIDATION.md` and the validation bundle
distributed with the frozen source candidate.
