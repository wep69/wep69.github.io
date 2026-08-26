# Ordinal Outcomes in Agricultural Experiments

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
ordered disease severity, cumulative and sequential models, thresholds,
links, category probabilities, practical contrasts, and
proportional-odds interpretation. The statistical backend does not
define the scientific question. The experimental units, observation
process, likelihood, prior, and estimand must be stated before
computation. See Agresti (2010).

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

dat <- bayes_teaching_data("ordinal")
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

des <- bayes_ordinal(dat, "severity", ~ treatment, group = "block", family = "cumulative", link = "logit")
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

### Ordinal categories are ordered, not equally spaced

Disease severity scores, visual ratings, and quality classes often have
a natural order but no defensible claim that the distance from category
1 to 2 equals the distance from 3 to 4. Ordinal likelihoods model
cumulative or sequential category probabilities rather than treating the
labels as metric measurements. See Agresti (2010).

### Threshold interpretation

In cumulative models, thresholds partition an underlying latent scale.
Treatment coefficients shift the latent location relative to those
thresholds. Direct interpretation is often clearest on the response
scale through posterior category probabilities.

``` r

# After fitting locally:
# pr <- bayes_ordinal_probabilities(ord_fit, newdata = new_treatments)
# pr
```

### Link-function sensitivity

Logit and probit links often lead to similar scientific conclusions but
differ in latent-scale parameterization. Complementary log-log can be
useful for asymmetric transition behavior. Compare links only when they
represent plausible observation processes.

### Threshold structure

`threshold = "flexible"` estimates category thresholds without equal
spacing. `threshold = "equidistant"` imposes a stronger structure and
should be used only with a defensible reason.

### Practical effects for ordinal outcomes

A practically meaningful effect may be expressed as a change in the
probability of severe categories rather than a latent coefficient
threshold. Report response-scale quantities whenever possible.

## Validation targets for the frozen ordinal dataset

Verify locally that:

1.  categories preserve their intended order;
2.  at least three categories are represented;
3.  predicted category probabilities sum to one for every posterior
    draw/row;
4.  treatment-induced shifts in severe-category probability are
    recovered;
5.  posterior predictive category frequencies resemble observed
    frequencies;
6.  threshold posteriors remain ordered and identified;
7.  conclusions are checked under at least one defensible alternate
    link; and
8.  response-scale reporting is consistent with latent-scale
    coefficients.

## Reporting example structure

State the category order, ordinal family and link, threshold structure,
treatment and grouping effects, priors, posterior category probabilities
for scientifically relevant contrasts, threshold uncertainty, sampler
diagnostics, posterior predictive category frequencies, and
link/threshold sensitivity analyses.

## Advanced exercises

1.  Fit cumulative-logit and cumulative-probit models.
2.  Compare predicted probability of the highest severity category by
    treatment.
3.  Calculate the probability of severity at or above a prespecified
    action threshold.
4.  Explain why treating severity 1–5 as Gaussian may be misleading.
5.  Compare flexible and equidistant thresholds.
6.  Interpret a positive latent treatment coefficient on the chosen
    category coding.
7.  Recode categories in reverse order and predict how coefficient signs
    change.
8.  Design a practical-effect definition on the probability scale.
9.  Discuss how repeated ordinal observations would extend the
    hierarchy.
10. Draft a results paragraph using category probabilities rather than
    only latent coefficients.

Agresti, Alan. 2010. *Analysis of Ordinal Categorical Data*. 2nd ed.
Wiley. <https://doi.org/10.1002/9780470594001>.
