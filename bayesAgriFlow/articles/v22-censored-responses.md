# Censored Responses and Detection Limits

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
left, right, and interval censoring, detection limits, continuous
likelihoods, latent response interpretation, posterior prediction, and
sensitivity to distribution choice. The statistical backend does not
define the scientific question. The experimental units, observation
process, likelihood, prior, and estimand must be stated before
computation. See B "urkner (2018).

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

dat <- bayes_teaching_data("censored")
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

des <- bayes_censored(dat, "residue", ~ treatment, "censor", group = "block", family = "lognormal")
bayes_censoring_summary(des)
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

### Censoring is not deletion

A left-censored observation below a laboratory detection limit still
contains information: the latent value is known to lie below a boundary.
Replacing it by zero, half the detection limit, or deleting the row
changes the likelihood and can distort uncertainty.

The censoring indicator records whether each value is observed exactly,
left censored, right censored, or interval censored. For interval
censoring, the response gives the lower bound and `censor_upper` gives
the upper bound.

### Choosing a response family

A positive chemical concentration may be better represented by a
lognormal, Gamma, or Weibull likelihood than by a Gaussian model. The
family should be justified by the measurement process and posterior
predictive behavior, not selected only by a single fit statistic.

``` r

des_c <- bayes_censored(
  bayes_teaching_data("censored"),
  "residue", ~ treatment, "censor",
  group = "block", family = "lognormal"
)
bayes_censoring_summary(des_c)
```

### Detection limits and treatment effects

When censoring differs by treatment, naive substitution methods can
create artificial treatment differences. The censored likelihood allows
treatment effects to be estimated while respecting the partial
information in censored rows.

### Posterior prediction under censoring

Posterior predictive checks should consider both the latent response
distribution and the observed censoring process. A model can match the
uncensored values yet fail to reproduce the expected fraction below the
detection threshold.

## Validation targets for the frozen censored dataset

Local validation should verify that:

1.  censoring labels are valid;
2.  all interval upper bounds exceed lower bounds when interval
    censoring is used;
3.  positive-family support is respected;
4.  the observed proportion censored is reproduced plausibly;
5.  treatment effects are recoverable from the simulated truth;
6.  uncertainty increases relative to an otherwise identical fully
    observed dataset;
7.  a naive substitution analysis is not silently used; and
8.  posterior conclusions are checked across defensible
    positive-response families.

## Reporting example structure

Report the censoring rule and boundary, number and proportion of
observations in every censoring category, response family, treatment and
grouping structure, priors, posterior treatment effects, predictive
checks of both value distribution and censoring frequency, and
sensitivity to alternative response distributions.

## Advanced exercises

1.  Compare lognormal and Gaussian censored models.
2.  Compare the censored model with a half-LOD substitution analysis and
    explain the difference.
3.  Calculate treatment-specific posterior predicted probabilities of
    being below the detection limit.
4.  Simulate a higher detection limit and assess loss of information.
5.  Explain the distinction between left censoring and truncation.
6.  Construct an interval-censored example.
7.  Identify which priors most affect tail behavior.
8.  Discuss how varying laboratory detection limits could be
    represented.
9.  Write a methods statement that makes the censoring mechanism
    reproducible.
10. Explain why a posterior interval for treatment effect is not a
    statement about an individual censored sample.

B "urkner, Paul-Christian. 2018. “Advanced Bayesian Multilevel Modeling
with the r Package Brms.” *The R Journal* 10 (1): 395–411.
<https://doi.org/10.32614/RJ-2018-017>.
