# Changelog

## bayesAgriFlow 1.0.0

### First stable consolidated release

- Consolidates all implemented functionality from development versions
  0.1.0 through 0.6.0 into the initial stable 1.x API.
- Confirms 0.6.0 as the final pre-1.0 development release; the
  previously reserved 0.7.0 development release is not used.
- Freezes the design -\> prior -\> fit -\> diagnose -\>
  summarize/evidence -\> predict/compare -\> decide -\> report workflow
  as the principal public contract.
- Carries forward 95 exported functions, 17 registered S3 methods, 30
  extensive English vignettes, 31 simulated teaching datasets, and 29
  double-verified methodological references.
- Consolidates classical randomized designs, split-plot families,
  quantitative regression and optima, mixed/repeated/GLMM workflows,
  flexible models, complex experimental systems, model uncertainty,
  projection predictive selection, and Bayesian decision analysis.
- Adds final 1.0 release documentation, an API stability policy,
  consolidation audit, package citation metadata, and a 1.0-specific
  runtime validation harness.
- Does not claim runtime validation in environments where R/CmdStan are
  unavailable; the frozen 1.0 source candidate must still pass the
  documented R-built tarball release gates.

### Fixes (local runtime validation, 2026-08-25)

- [`bayes_multivariate()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_multivariate.md)
  now evaluates the residual-correlation family compatibility gate
  before the response-versus-family validator. Previously, declaring a
  count family together with continuous data made the documented
  “Residual correlation is enabled only for Gaussian/Student” error
  unreachable; both validations remain in force and only their order
  changed.
- Confirmed maintainer address recorded in DESCRIPTION; package URL now
  includes the published documentation site.

## bayesAgriFlow 0.6.0

### Model uncertainty, predictive reduction, and Bayesian decisions

- Added predictive model weights based on PSIS-LOO stacking,
  pseudo-BMA+, and pseudo-BMA.
- Added formal Bayesian model-averaging weights from marginal
  likelihoods and prior model probabilities.
- Added posterior predictive mixtures that retain between-model
  uncertainty.
- Added projection predictive variable selection through optional
  `projpred`, including cross-validated search, ranking, suggested size,
  projection, and projected prediction.
- Added a transparent utility layer for agronomic outcomes, action
  costs, and custom nonlinear utility functions.
- Added Bayes actions based on expected utility, posterior probability
  of optimality, and expected regret.
- Added expected value of perfect information (EVPI) and a generic
  expected value of sample information (EVSI) interface with an explicit
  nested-simulation contract.
- Added direct treatment-decision workflows and sensitivity analysis
  over output-value scenarios.
- Added 3 frozen simulated teaching/validation datasets and 4 extensive
  vignettes devoted to model uncertainty, projection predictive
  selection, decision analysis, and value of information.
- Extended the reporting and validation infrastructure to keep
  posterior-model probabilities, predictive weights, decision utility,
  regret, and information value conceptually distinct.

## bayesAgriFlow 0.5.0

### Complex experimental systems and imperfect observations

- Added spatial field-trial models with two-dimensional Gaussian-process
  terms.
- Added hierarchical genotype-by-environment workflows with explicit
  genotype, environment, interaction, and environment-specific block
  roles.
- Added multivariate Bayesian models with response-specific families and
  optional residual correlation when supported.
- Added predictor measurement-error models using the current brms `mi()`
  formulation rather than the soft-deprecated `me()` interface.
- Added one-step joint models for continuous predictors with missing
  values, with optional response imputation.
- Added left-, right-, and interval-censored continuous-response
  workflows.
- Added cumulative, continuation-ratio, stopping-ratio, and
  adjacent-category ordinal models.
- Added finite mixture models with posterior component-membership
  probabilities.
- Added distributional regression for scale, shape, precision, and
  related distributional parameters.
- Added 9 frozen simulated teaching/validation datasets and 8 extensive
  vignettes.
- Extended validation gates for spatial recovery, G x E variance
  components, multivariate residual correlation, missing-data recovery,
  censoring, ordinal probabilities, mixture membership, and
  distributional parameters.

## bayesAgriFlow 0.4.0

- Added nonlinear Bayesian agronomic response templates for
  Michaelis-Menten, Mitscherlich, four-parameter logistic, and Gompertz
  curves through brms/Stan.
- Added Bayesian smoothing splines through brms smooth terms with
  explicit basis dimensions and optional factor-specific smooths.
- Added one- and multi-predictor Gaussian-process regression with
  exponentiated quadratic, Matern, and exponential kernels, including
  optional Hilbert-space approximations.
- Added Rubin’s Bayesian bootstrap for posterior means or medians and
  grouped pairwise contrasts with ROPE and meaningful-effect
  probabilities.
- Added an optional PyMC/PyMC-BART backend for Bayesian additive
  regression trees, including response-scale prediction, ArviZ
  diagnostics, PSIS-LOO, and normalized variable-inclusion summaries.
- Added explicit PyMC environment inspection and opt-in setup functions;
  Python remains entirely optional and is never installed on package
  load.
- Added four frozen simulated teaching datasets for nonlinear, spline,
  Gaussian- process, and BART workflows.
- Added four extensive English vignettes for flexible response models,
  Bayesian bootstrap inference, and PyMC/PyMC-BART workflows.
- Extended the capability registry, posterior prediction, diagnostics,
  LOO assessment, source validation, and forward architecture for future
  spatial, multivariate, measurement-error, censoring, and missing-data
  models.

## bayesAgriFlow 0.3.0

- Added general Gaussian and Student-t mixed models with random
  intercepts, random slopes, nested grouping chains, and crossed
  grouping factors.
- Added heterogeneous residual SD structures for Gaussian and Student-t
  mixed models.
- Added repeated-measures designs with explicit subject and time roles,
  subject-specific time slopes, and optional Gaussian AR(1) residual
  dependence.
- Added split-split-plot and strip-plot design objects with explicit
  experimental-unit identifiers and structural audits.
- Added GLMM support for Bernoulli, binomial, Poisson,
  negative-binomial, Gamma, lognormal, and Beta responses through the
  optional `brms` backend.
- Added explicit binomial trial counts and count exposure offsets.
- Added
  [`bayes_variance_components()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_variance_components.md)
  and
  [`bayes_random_effects()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_random_effects.md)
  for hierarchical posterior summaries.
- Added
  [`bayes_predict()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_predict.md)
  for unified response-scale posterior mean and predictive
  distributions.
- Added
  [`bayes_loo()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_loo.md)
  and
  [`bayes_compare_models()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compare_models.md)
  for PSIS-LOO predictive assessment.
- Extended the frequentist teaching crosswalk to mixed and selected GLMM
  structures while explicitly warning when AR or heterogeneous residual
  structures are not reproduced.
- Added seven new frozen simulated teaching datasets for mixed,
  repeated, split-split, strip-plot, count GLMM, binomial GLMM, and
  heterogeneous-residual workflows.
- Expanded the quantitative-regression and optimum vignettes.
- Added four extensive vignettes covering mixed models, repeated
  measures, split-split/strip-plot designs, and non-Gaussian GLMMs.
- Added and double-verified mixed-model and GLMM references.

## bayesAgriFlow 0.2.0

- Added quantitative-factor regression and polynomial regression
  (degrees 1–3).
- Added ANCOVA with optional treatment-by-covariate interaction.
- Added qualitative-by-quantitative regression.
- Added posterior biological optimum over an explicit experimental
  domain.
- Added posterior economic optimum with fixed output-price and linear
  input-cost assumptions.
- Added three frozen simulated teaching datasets for dose response,
  ANCOVA, and qualitative-by-quantitative models.
- Added two extensive vignettes covering quantitative models and
  Bayesian optima.
- Preserved the design-first API and CmdStanR parallel-computing layer
  introduced in 0.1.0.

## bayesAgriFlow 0.1.0

- Added unified design objects for CRD, RCBD, Latin square, factorial,
  and Gaussian split-plot experiments.
- Added design auditing with explicit experimental-unit checks.
- Added a conjugate Gaussian teaching engine for fixed-effect designs.
- Added optional `BayesFactor` evidence and optional `brms`/Stan
  estimation.
- Added `cmdstanr` computation controls, explicit CPU budgets, parallel
  chains, and optional within-chain threading.
- Added posterior summaries, treatment contrasts, direction
  probabilities, ROPE, equivalence, and meaningful-effect probabilities.
- Added prior predictive checks, posterior predictive checks, and
  diagnostic summaries.
- Added a frequentist crosswalk for teaching only.
- Added five frozen simulated agronomic teaching datasets.
- Added eight extensive English vignettes and reference-verification
  assets.
- Added developer validation scripts and local release instructions.
