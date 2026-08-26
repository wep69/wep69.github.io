# Spatial Field Trials with Bayesian Gaussian Processes

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
spatial randomization, coordinates, residual field gradients, Gaussian
processes, treatment adjustment, block hierarchy, posterior maps,
interpolation limits, and diagnostics. The statistical backend does not
define the scientific question. The experimental units, observation
process, likelihood, prior, and estimand must be stated before
computation. See Diggle et al. (1998).

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

dat <- bayes_teaching_data("spatial")
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

des <- bayes_spatial_field(dat, "yield", "x", "y", ~ treatment, block = "block")
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

### Coordinate systems and field geometry

The package assumes that `x_coord` and `y_coord` are numeric coordinates
on a scale that can be interpreted spatially. They may be plot-center
coordinates in metres, projected easting/northing, or another internally
consistent field coordinate system. Geographic longitude and latitude
should not be treated as an ordinary Euclidean plane across large
regions without a defensible projection.

For the bundled field trial, each coordinate pair identifies one plot
location. Replicate measurements collected from the same plot should
first be represented as repeated or nested observations rather than
disguised as distinct spatial locations.

### What the Gaussian process is adjusting

The GP term represents residual spatial structure after the declared
treatment and block effects. It is not a substitute for the
randomization. If the trial was blocked, block remains in the model
because it belongs to the design; the GP captures remaining continuous
spatial dependence.

Useful posterior quantities include:

- treatment contrasts after spatial adjustment;
- the posterior standard deviation of the spatial process;
- the posterior length scale;
- response-scale fitted values at observed plots;
- predicted field surfaces inside the sampled region; and
- uncertainty that increases in sparsely observed parts of the field.

### Kernel sensitivity

A covariance kernel encodes how similarity decays with spatial
separation. The scientific conclusion should not depend on choosing a
kernel merely because it gives the smallest information criterion.
Compare a small set of defensible alternatives and inspect posterior
predictive behavior, length-scale identification, and treatment
stability.

``` r

des_exp <- bayes_spatial_field(dat, "yield", "x", "y", ~ treatment,
                               block = "block", cov = "exp_quad")
des_m32 <- bayes_spatial_field(dat, "yield", "x", "y", ~ treatment,
                               block = "block", cov = "matern32")
```

### Spatial treatment contrasts

A treatment contrast remains a comparison of treatment-level response
distributions. Spatial adjustment changes the conditioning model, not
the definition of the treatment effect. Report the contrast, its
credible interval, probability of direction, and a scientifically
justified meaningful-difference probability when appropriate.

### Interpolation is not extrapolation

Predictions between observed plots are interpolation. Predictions beyond
the convex region represented by the field coordinates are extrapolation
and depend increasingly on the kernel and prior. Maps should distinguish
the sampled domain from any extrapolated area.

## Validation targets for the frozen spatial dataset

During local validation, verify that:

1.  all coordinate pairs are unique;
2.  treatment assignment remains balanced enough for the teaching
    design;
3.  the non-spatial treatment coefficients are recoverable under
    simulation;
4.  the spatial-process standard deviation is finite and identified;
5.  the length-scale posterior does not collapse at an arbitrary
    boundary;
6.  posterior predictive residual patterns are weaker than in a
    non-spatial comparator;
7.  treatment conclusions are stable across at least two defensible
    kernels; and
8.  1-CPU and N-CPU fits agree within Monte Carlo uncertainty.

## Reporting example structure

A reproducible methods paragraph should state that the response was
modeled with treatment effects, the declared block hierarchy, and a
two-dimensional GP over plot coordinates; identify the covariance kernel
and whether coordinates were scaled; report priors for fixed effects and
GP parameters; identify `brms`, CmdStan, chain settings, seed, and CPU
configuration; and state how convergence and posterior predictive
adequacy were assessed.

## Advanced exercises

1.  Fit the same trial without a GP and compare treatment posteriors.
2.  Compare exponentiated-quadratic and Matérn covariance structures.
3.  Plot posterior median predictions only at observed coordinates.
4.  Produce a second map showing posterior predictive SD.
5.  Define a yield difference that would matter agronomically and
    compute its posterior probability.
6.  Explain why a visually smooth field map does not validate causal
    treatment effects.
7.  Identify which plots exert the greatest leverage on the inferred
    spatial scale.
8.  Discuss how irregular plot spacing would change interpretation.
9.  Explain why latitude/longitude may need projection.
10. Write a BARG-compatible reporting block for the final spatial model.

Diggle, Peter J., Jonathan A. Tawn, and Rafat A. Moyeed. 1998.
“Model-Based Geostatistics.” *Journal of the Royal Statistical Society:
Series C (Applied Statistics)* 47 (3): 299–350.
<https://doi.org/10.1111/1467-9876.00113>.
