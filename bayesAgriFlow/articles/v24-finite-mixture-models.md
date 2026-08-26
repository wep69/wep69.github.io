# Finite Mixture Models for Heterogeneous Biological Responses

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
latent subpopulations, component identification, informative priors,
mixture weights, membership probabilities, posterior predictive checks,
and label-switching risks. The statistical backend does not define the
scientific question. The experimental units, observation process,
likelihood, prior, and estimand must be stated before computation. See
McLachlan and Peel (2000).

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

dat <- bayes_teaching_data("mixture")
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

des <- bayes_mixture(dat, "seed_weight", ~ treatment, components = c("gaussian", "gaussian"))
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

### Why a mixture model

A finite mixture assumes that the observed distribution can arise from
more than one latent component. In seed or plant-trait data, components
may represent latent subpopulations, but biological labels should not be
assigned automatically from a statistical decomposition. See McLachlan
and Peel (2000).

### Identification and label switching

Mixture likelihoods are symmetric under component relabeling unless
identifying constraints or informative priors are used. Version 1.0.0
exposes component ordering and places separated intercept priors based
on response quantiles as a practical starting point. These choices must
be inspected through sensitivity analysis.

### Component membership is probabilistic

[`bayes_mixture_membership()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_mixture_membership.md)
returns posterior membership probabilities. A row with probabilities
0.55 and 0.45 is uncertain and should not be forced into a deterministic
class without a decision rule and associated loss.

``` r

# After fitting locally:
# memb <- bayes_mixture_membership(mix_fit)
# memb
```

### Number of components

The number of components should be scientifically and predictively
justified. Adding components always increases flexibility and can create
weakly identified tiny components. Use posterior predictive checks,
prior sensitivity, and predictive comparison rather than a ritual search
for the largest marginal likelihood.

### Covariates in component means

A shared main formula in
[`bayes_mixture()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_mixture.md)
allows predictors to influence component-specific mean parameters
through the brms mixture parameterization. Advanced versions may expose
different formulas by component; version 1.0.0 intentionally keeps the
unified constructor simpler.

## Validation targets for the frozen mixture dataset

Local validation should verify that:

1.  both simulated components are represented;
2.  priors break unhelpful label symmetry without dominating the
    likelihood;
3.  component means are recovered near the frozen simulation truth;
4.  mixture weights are recoverable;
5.  posterior membership is high for well-separated observations and
    uncertain near overlap;
6.  posterior predictive density reproduces multimodality;
7.  two-component conclusions are stable to reasonable prior changes;
    and
8.  adding an unnecessary third component is diagnosed rather than
    celebrated as extra detail.

## Reporting example structure

Report the rationale for a mixture, component families, component count,
identification/order convention, component-specific priors, posterior
component locations and scales, mixture weights, membership uncertainty,
predictive checks, sensitivity to priors and component number, and a
warning against equating latent components with biological classes
without external evidence.

## Advanced exercises

1.  Fit two- and three-component Gaussian mixtures.
2.  Inspect membership probabilities for observations near component
    overlap.
3.  Change intercept priors and diagnose label sensitivity.
4.  Compare a Gaussian mixture with a Student-t sensitivity model.
5.  Explain why hard clustering discards posterior uncertainty.
6.  Calculate the posterior probability that an observation belongs to
    component 2.
7.  Examine whether treatment shifts both component means similarly.
8.  Discuss biological validation needed before naming components.
9.  Identify signs of a redundant component.
10. Draft a transparent mixture-model reporting paragraph.

McLachlan, Geoffrey, and David Peel. 2000. *Finite Mixture Models*.
Wiley. <https://doi.org/10.1002/0471721182>.
