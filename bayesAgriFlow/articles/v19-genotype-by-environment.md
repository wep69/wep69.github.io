# Hierarchical Genotype-by-Environment Models

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
multi-environment trials, genotype and environment roles, G x E
interaction, random versus fixed components, partial pooling, variance
components, stability, and prediction. The statistical backend does not
define the scientific question. The experimental units, observation
process, likelihood, prior, and estimand must be stated before
computation. See Malosetti et al. (2013).

## Learning objectives

After completing this vignette, the reader should be able to:

1.  identify the experimental and observational units;
2.  distinguish the biological process from the observation process;
3.  construct the corresponding `bayes_design`;
4.  choose priors on scientifically meaningful scales;
5.  run prior predictive checks before fitting;
6.  fit the model with `brms` and CmdStanR when available;
7.  review R-hat, ESS, MCSE, divergences and posterior predictive
    checks;
8.  interpret posterior effects without reducing the analysis to one
    threshold;
9.  evaluate sensitivity to important modeling choices; and
10. report the analysis reproducibly.

## Teaching data

``` r

dat <- bayes_teaching_data("gxe")
str(dat)
head(dat)
```

The bundled data are simulated and frozen for teaching and validation.
They are not empirical evidence. Their known data-generating structure
makes them useful for parameter recovery and workflow tests.

## Design first

A Bayesian analysis does not repair an incorrectly identified
experimental unit. Before writing a formula, document the randomization,
repeated observations, grouping factors, measurement process, and
scientifically meaningful contrasts.

## Core package workflow

The 1.0.0 complex-system workflow has the same high-level stages as
simpler package analyses:

``` text
scientific question
  -> design and observation audit
  -> likelihood and prior
  -> prior predictive check
  -> posterior computation
  -> sampler diagnostics
  -> posterior predictive check
  -> effects / predictions / decisions
  -> sensitivity analysis
  -> reproducible report
```

## Constructing the model

The exact constructor for this vignette is shown below. Heavy models are
not evaluated during routine vignette building.

``` r

des <- bayes_gxe(dat, "yield", "genotype", "environment", "block")
bayes_design_audit(des)
bayes_plan(des)
```

## Priors and prior predictive reasoning

Priors should be checked on the scale of the variables and on the scale
of generated observations. In complex models, default priors can be
convenient starting points, but a convenient prior is not automatically
a scientifically defensible prior.

``` r

pr <- bayes_prior("regularizing")
pr
```

For Stan-based complex models, the exact prior predictive distribution
should be generated through the brms path with prior-only sampling. The
native conjugate simulator is intentionally restricted to simpler
Gaussian teaching models.

## CmdStanR computation

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 1000,
  seed = 20260824
)
ctrl
```

A larger CPU count does not compensate for poor geometry, weak
identification, or an inappropriate likelihood. Parallel chains improve
elapsed time but not the conceptual quality of the model.

## Fitting locally

``` r

fit <- bayes_fit(des, engine = "brms", prior = pr, compute = ctrl)
fit
bayes_summary(fit)
```

## Sampler diagnostics

``` r

diag <- bayes_diagnose(fit)
diag
```

Interpretation should stop when serious divergences or convergence
failures remain unresolved. ESS, R-hat and MCSE answer computational
questions, not scientific questions.

## Posterior predictive checks

``` r

ppc <- bayes_pp_check(fit, ndraws = 200)
ppc
```

Posterior predictive checks ask whether replicated data from the fitted
model reproduce features of the observed data that matter for the
scientific problem. They do not prove that the model is true.

## Posterior prediction

``` r

pred <- bayes_predict(fit, type = "mean")
head(pred$summary)
```

Predictions should be distinguished from latent parameters and from
treatment contrasts. Extrapolation beyond the design region deserves
separate scrutiny.

## Practical relevance and effect interpretation

ROPE, probability of direction, meaningful-effect thresholds and Bayes
factors answer different questions. A large posterior probability of a
positive effect does not by itself establish practical importance.
Conversely, a posterior concentrated inside a scientifically justified
ROPE supports practical equivalence under that model and prior.

## Model comparison

PSIS-LOO can compare predictive adequacy among compatible fitted models.
Bayes factors compare marginal likelihoods under explicitly specified
priors. Neither should be used as an automatic model-selection ritual.

``` r

if (FALSE) {
  loo1 <- bayes_loo(fit)
  loo1
}
```

## Sensitivity analysis

At minimum, sensitivity analysis should consider:

- prior scale and prior location;
- likelihood family;
- dependency or hierarchy specification;
- influential design assumptions;
- alternative scientifically plausible definitions of practical
  importance; and
- computational stability.

## Reporting checklist

A report should state the response and predictors, experimental units,
observation model, hierarchy, priors and rationale, software and
backend, chain settings, posterior summaries, diagnostic evidence,
posterior predictive checks, sensitivity analyses, and limitations.
BARG-style reporting remains applicable to these more complex models.

## Common mistakes

1.  treating the backend as the model;
2.  ignoring the experimental unit;
3.  choosing a likelihood by habit rather than by the observation
    process;
4.  interpreting a Bayes factor as an effect size;
5.  treating a credible interval as a frequentist confidence interval;
6.  interpreting convergence as model adequacy;
7.  reporting only posterior means;
8.  failing to check prior predictive behavior;
9.  hiding data exclusions or missingness handling; and
10. presenting predictions outside the data-supported region without
    qualification.

## Reproducibility block

``` r

sessionInfo()
bayes_compute_info(ctrl)
```

## Starter script

The following skeleton is intentionally explicit.

``` r

# 1. Inspect data
# 2. Define design
# 3. Audit design
# 4. Specify priors
# 5. Perform prior predictive checks
# 6. Configure CmdStanR
# 7. Fit posterior
# 8. Diagnose computation
# 9. Perform posterior predictive checks
# 10. Summarize effects and predictions
# 11. Run sensitivity analyses
# 12. Generate a reproducible report
```

## Advanced extensions

Version 1.0.0 exposes this data structure to the current
model-uncertainty and decision layers, including predictive comparison,
model averaging where appropriate, projection-predictive tools for
compatible reference models, and utility-based decisions, without making
unsupported automatic choices.

## Exercises

1.  Change the prior scale and explain what changes in prior predictive
    space.
2.  Fit a scientifically plausible alternative model and compare
    posterior predictive behavior.
3.  Define a practical threshold in the units of the response.
4.  Identify which conclusions are sensitive to the structural
    assumptions.
5.  Write a methods paragraph that another analyst could reproduce.
6.  State what evidence would make you reject the current model
    structure.
7.  Explain which quantities are estimates, which are predictions, and
    which are decisions.
8.  Describe what additional data would most reduce scientific
    uncertainty.

## References

The package bibliography provides the methodological references cited
above. Software versions and exact backend configuration should
additionally be reported for every fitted analysis.

### Design audit questions

- What was randomized?
- At what level was treatment assigned?
- Which observations share latent causes?
- Which variables describe the observation process rather than biology?
- Which contrasts were planned before seeing the results?

### Prior audit questions

- Is the prior plausible on the original response scale?
- Does the prior permit impossible biological values?
- Are group-level SD priors compatible with plausible heterogeneity?
- Are weakly identified parameters receiving enough regularization?
- Would a reasonable alternative prior alter the scientific conclusion?

### Posterior audit questions

- Are all reported effects identified by the design?
- Are intervals sufficiently precise for the decision?
- Do important posterior correlations reveal weak identification?
- Are transformed response-scale quantities reported when link functions
  are used?
- Are derived quantities accompanied by uncertainty?

### Predictive audit questions

- Do replicated data reproduce central tendency?
- Do they reproduce dispersion and tails?
- Do they reproduce group-to-group heterogeneity?
- Do they reproduce the feature that motivated the complex model?
- Where does prediction become extrapolation?

### Communication audit questions

- Is uncertainty visible?
- Are priors named and justified?
- Are software and seeds recorded?
- Are failed model variants documented when relevant?
- Is the frequentist crosswalk clearly labeled as pedagogical?

## Model-specific technical notes

### The G x E estimand

Genotype-by-environment analysis should start by deciding whether
inference targets the observed genotypes, a broader genotype population,
the observed environments, or a target population of environments. The
fixed/random labels in
[`bayes_gxe()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_gxe.md)
encode part of that scientific scope and must not be selected
mechanically.

The default teaching specification treats environments as fixed
conditions of interest, genotype effects as varying, and
genotype-by-environment deviations as varying. Blocks are nested within
environment because a block in one environment is not the same
experimental unit as a block carrying the same label elsewhere.

### Variance decomposition

Posterior variance components can describe:

- among-genotype heterogeneity;
- genotype-by-environment heterogeneity;
- environment-specific block heterogeneity; and
- residual within-cell variation.

These components should be reported with credible intervals. A component
near zero is still uncertain; avoid replacing the posterior with a
binary variance-component test.

### Genotype ranking is environment-dependent

A global expected rank can hide cross-over interaction. Inspect genotype
predictions within each environment and quantify uncertainty in pairwise
differences. `P(best)` may be useful descriptively, but it is sensitive
to the candidate set and should not replace effect magnitudes.

``` r

# After fitting locally:
# bayes_predict(gxe_fit, newdata = prediction_grid, type = "mean")
# bayes_gxe_components(gxe_fit)
```

### Fixed versus random genotype effects

Treating genotype as fixed is appropriate when inference is
intentionally limited to the named entries. Treating genotype as varying
supports partial pooling and population-level variance estimation.
Neither parameterization is universally superior; the choice follows the
inferential target.

### Environment inference

Observed environments may represent specific sites/years or a sample
from a broader target environment population. Version 1.0.0 exposes
fixed or random environment effects, but it does not automatically build
environmental covariate regressions or factor-analytic covariance
structures. Those remain explicit future extensions.

## Validation targets for the frozen G x E dataset

Local validation should verify:

1.  every genotype occurs in every teaching environment;
2.  block identifiers are interpreted within environment;
3.  genotype variance is recovered with correct order of magnitude;
4.  G x E variance is non-zero in the simulated truth and recoverable;
5.  environment means are reproduced by posterior predictions;
6.  genotype contrasts change appropriately across environments;
7.  partial pooling is visible for genotype effects; and
8.  conclusions are compatible across CPU configurations within MCSE.

## Reporting example structure

Report the target population of genotypes and environments, the fixed or
varying status of each effect, the environment-specific blocking
structure, priors on variance components, posterior summaries of
genotype and G x E variation, environment-specific genotype predictions,
sampler diagnostics, posterior predictive checks, and the limits of
generalization beyond the sampled environments.

## Advanced exercises

1.  Refit with genotype fixed and compare the estimand.
2.  Refit with environment varying and explain the changed target
    population.
3.  Calculate environment-specific posterior genotype contrasts.
4.  Identify a cross-over interaction in posterior predictions.
5.  Compare genotype variance with G x E variance.
6.  Define a practically meaningful yield difference across
    environments.
7.  Explain why averaging across environments can obscure instability.
8.  Propose environmental covariates for a future reaction-norm model.
9.  Explain what additional model would be needed for factor-analytic G
    x E covariance.
10. Draft a reproducible G x E methods and results paragraph.

Malosetti, Marcos, Jean-Marcel Ribaut, and Fred A. van Eeuwijk. 2013.
“The Statistical Analysis of Multi-Environment Data: Modeling
Genotype-by-Environment Interaction and Its Genetic Basis.” *Frontiers
in Physiology* 4: 44. <https://doi.org/10.3389/fphys.2013.00044>.
