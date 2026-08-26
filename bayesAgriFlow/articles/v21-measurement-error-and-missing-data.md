# Measurement Error and Joint Missing-Data Models

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
known measurement error, latent predictors, missingness mechanisms,
one-step imputation, uncertainty propagation, sensitivity, and
transparent reporting. The statistical backend does not define the
scientific question. The experimental units, observation process,
likelihood, prior, and estimand must be stated before computation. See
Little and Rubin (2019).

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

dat <- bayes_teaching_data("measurement_error")
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

des <- bayes_measurement_error(dat, "yield", ~ soil_n + cultivar, "soil_n", "soil_n_se", ~ organic_matter)
bayes_design_audit(des)
bayes_plan(des)

mis <- bayes_teaching_data("missing")
mis_des <- bayes_missing(mis, "yield", ~ soil_n + organic_matter, "soil_n", list(soil_n = ~ organic_matter))
bayes_design_audit(mis_des)
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

### Measurement error and missingness are different observation problems

Known measurement error says that an observed predictor is noisy around
an unobserved latent value. Missingness says that the predictor value
was not observed for some experimental units. The `mi()` framework can
express both, but the scientific assumptions remain different.

[`bayes_measurement_error()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_measurement_error.md)
requires an observation-level standard-error column and jointly
estimates the latent predictor.
[`bayes_missing()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_missing.md)
requires an explicit auxiliary model for every continuous predictor that
is imputed within the joint model.

### Why complete-case analysis can change the estimand

Rows with missing predictors may differ systematically from complete
rows. Dropping them can reduce precision and, under some mechanisms,
bias inference. One-step joint modeling keeps the missing-data
assumptions visible in the model rather than hiding them in
preprocessing.

### The imputation model is part of the analysis

An imputation model needs predictors that make scientific sense. It
should not include variables simply because they improve apparent fit.
The missing-data model and the substantive response model form one joint
probabilistic system and should be reported together. See Little and
Rubin (2019).

``` r

des_missing <- bayes_missing(
  bayes_teaching_data("missing"),
  response = "yield",
  fixed = ~ soil_n + organic_matter,
  impute = "soil_n",
  imputation_models = list(soil_n = ~ organic_matter)
)
```

### Known standard errors are not estimated standard deviations

For measurement error, `predictor_se` must describe known
observation-level measurement uncertainty. Passing a sample SD of the
predictor as though it were measurement error changes the model and is
not justified.

### Missingness assumptions

Version 1.0.0 does not infer the missingness mechanism automatically.
Analysts should discuss whether missingness is plausibly ignorable given
the variables included in the joint model. Sensitivity analysis is
necessary when missing-not-at-random mechanisms are scientifically
plausible.

### No discrete imputation in this release

The one-step constructor is intentionally limited to continuous imputed
predictors because Stan does not estimate unknown discrete parameters
directly. Discrete missing-data problems need different formulations or
external multiple-imputation strategies.

## Validation targets for the frozen imperfect-observation datasets

Verify locally that:

1.  known measurement-error SDs are positive and finite;
2.  the measured predictor is complete in the measurement-error example;
3.  missing predictors retain observed values for model identification;
4.  the joint missing-data model preserves all intended rows;
5.  latent predictor estimates correlate with the frozen simulation
    truth;
6.  uncertainty is wider than in an unrealistically error-free analysis
    when appropriate;
7.  posterior predictive checks are examined for both substantive and
    auxiliary responses; and
8.  conclusions are stable across plausible auxiliary-model
    specifications.

## Reporting example structure

For measurement error, report the source and interpretation of the known
standard errors, the latent predictor model, substantive response model,
priors, and posterior sensitivity. For missing data, report the amount
and pattern of missingness, variables included in each imputation
submodel, assumptions about the missingness mechanism, convergence of
all joint-model parameters, and sensitivity to alternative imputation
models.

## Advanced exercises

1.  Compare an error-free regression with the measurement-error model.
2.  Quantify attenuation of the soil-nitrogen slope in the naive model.
3.  Remove organic matter from the imputation model and compare
    uncertainty.
4.  Plot posterior latent soil nitrogen against the frozen simulation
    truth.
5.  Increase known measurement error and predict how the slope posterior
    should change.
6.  Explain why imputed values should not be treated as observed
    constants.
7.  Discuss a plausible missing-not-at-random mechanism for soil
    measurements.
8.  Design a sensitivity analysis for that mechanism.
9.  Explain why discrete missing treatment assignments are outside this
    constructor.
10. Write separate reporting paragraphs for measurement error and
    missingness.

Little, Roderick J. A., and Donald B. Rubin. 2019. *Statistical Analysis
with Missing Data*. 3rd ed. Wiley.
<https://doi.org/10.1002/9781119482260>.
