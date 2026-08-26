# Nonlinear Bayesian Response Models for Agronomy

## 1. Why nonlinear response curves matter

Agronomic responses frequently have parameters with direct biological
meaning. A crop may approach an asymptote as nutrient supply increases,
a disease response may follow a sigmoidal trajectory, and growth may
accelerate before reaching a plateau. In these settings, a polynomial
can interpolate the observed range but its coefficients often have weak
biological interpretation.

Version 1.0.0 therefore adds explicit nonlinear templates through
[`bayes_nonlinear()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_nonlinear.md).
The implementation uses `brms` and Stan so that uncertainty in every
curve parameter propagates to derived predictions and decisions. The
broader principle is consistent with Bayesian model specification: the
likelihood and priors should describe the scientific mechanism as
clearly as the available data permit (Bürkner 2017; Annis et al. 2017).

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  distinguish nonlinear parameterization from polynomial
    approximation;
2.  select among Mitscherlich, Michaelis-Menten, four-parameter
    logistic, and Gompertz templates;
3.  interpret the parameters of each curve on agronomic scales;
4.  recognize weak identifiability when the observed domain misses a
    plateau or inflection;
5.  choose scale-aware priors and inspect their predictive implications;
6.  fit the model with `brms` and `cmdstanr`;
7.  diagnose HMC/NUTS computation;
8.  obtain posterior predictions and credible bands;
9.  derive an optimum only when the scientific definition makes sense;
10. compare alternative response shapes without selecting a model only
    because one index is largest;
11. perform parameter-recovery simulation before trusting a new
    application; and
12. report the model in a reproducible form.

## 3. Available nonlinear templates

``` r

bayes_nonlinear_models()
#>              model                     parameters
#> 1 michaelis_menten                     asym, half
#> 2     mitscherlich              lower, asym, rate
#> 3        logistic4      lower, upper, ed50, scale
#> 4         gompertz lower, upper, rate, inflection
#>                                                       agronomic_use
#> 1              Saturating response to a positive quantitative input
#> 2                     Diminishing-return response with an asymptote
#> 3 Sigmoidal dose or growth response with lower and upper asymptotes
#> 4                      Asymmetric sigmoidal growth or dose response
```

The templates in version 1.0.0 are intentionally few and interpretable.
Additional nonlinear forms can be added in later releases without
changing the design object or posterior interface.

## 4. Frozen teaching data

``` r

d <- bayes_teaching_data("nonlinear")
head(d)
#>   replicate nitrogen    yield
#> 1         1        0 2.358962
#> 2         2        0 3.184540
#> 3         3        0 2.603671
#> 4         4        0 3.294562
#> 5         5        0 3.052583
#> 6         6        0 2.904700
summary(d)
#>    replicate      nitrogen       yield      
#>  Min.   :1.0   Min.   :  0   Min.   :2.359  
#>  1st Qu.:2.0   1st Qu.: 30   1st Qu.:5.851  
#>  Median :3.5   Median : 90   Median :7.647  
#>  Mean   :3.5   Mean   : 90   Mean   :7.069  
#>  3rd Qu.:5.0   3rd Qu.:150   3rd Qu.:8.543  
#>  Max.   :6.0   Max.   :180   Max.   :9.755
```

The data are simulated and frozen for teaching. They resemble a
nitrogen-response experiment and must not be interpreted as empirical
agronomic evidence.

## 5. Begin with the experimental domain

``` r

range(d$nitrogen)
#> [1]   0 180
```

A nonlinear curve can make numerically precise predictions outside the
observed domain while being scientifically unsupported there. Save the
observed domain before fitting and distinguish interpolation from
extrapolation in every figure and optimum calculation.

## 6. Mitscherlich response

The Mitscherlich template implemented here is

``` math
\mu(x)=L+(A-L)\{1-\exp(-r x)\},
```

where $`L`$ is the lower response level, $`A`$ is the asymptote, and
$`r>0`$ controls the approach to the asymptote. This form is useful for
monotone saturation when the asymptote is scientifically meaningful.

## 7. Specify the Mitscherlich design

``` r

des_m <- bayes_nonlinear(
  d,
  response = "yield",
  quantitative = "nitrogen",
  model = "mitscherlich"
)

des_m
#> <bayes_design>
#>   Type: Nonlinear Bayesian response model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 42
#>   quantitative: nitrogen
bayes_design_audit(des_m)
#> <bayes_design_audit>
#>   Design: Nonlinear Bayesian response model
#>   Status: PASS
bayes_plan(des_m)
#> <bayes_plan>
#>   Design: Nonlinear Bayesian response model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

The automatic route is `brms` because this is a nonlinear Stan model
rather than a conjugate Gaussian teaching model.

## 8. Priors are part of the curve

`bayesAgriFlow` creates scale-aware regularizing defaults, but
confirmatory work should replace them with priors justified from
agronomic knowledge or independent information. For positive rate
parameters, the prior is constrained to the scientifically admissible
domain.

A useful prior review asks:

- Can the asymptote take implausibly negative or extremely large values?
- Does the rate prior imply saturation before the first treatment level?
- Does the lower-response prior agree with baseline knowledge?
- Are parameter correlations likely to produce weak identifiability?
- Would an informed prior be legitimate, or would it reuse the same data
  twice?

## 9. Optional custom parameter priors

``` r

des_prior <- bayes_nonlinear(
  d, "yield", "nitrogen",
  model = "mitscherlich",
  parameter_priors = c(
    lower = "normal(3, 1)",
    asym  = "normal(10, 2)",
    rate  = "lognormal(-4, 0.7)"
  )
)
```

These numbers are illustrative only. Priors must be expressed in the
units and parameterization of the actual experiment.

## 10. CmdStan computation control

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  iter_warmup = 1000,
  iter_sampling = 2000,
  seed = 20260824
)
```

Parallel chains are the conservative default. Within-chain threading
should be enabled only when the compiled model can benefit from it and
after runtime validation.

## 11. Fit the nonlinear model

``` r

fit_m <- bayes_fit(
  des_m,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = ctrl
)
```

No posterior interpretation should precede sampler diagnostics.

## 12. Diagnose computation

``` r

bayes_diagnose(fit_m)
```

Inspect R-hat, bulk and tail effective sample sizes, Monte Carlo
standard errors, divergences, and treedepth behavior. A smooth-looking
fitted curve does not rescue a poorly explored posterior.

## 13. Posterior predictive adequacy

``` r

bayes_pp_check(fit_m)
```

Posterior predictive checking should target the scientific structure:
baseline response, rate of increase, upper-tail behavior, and variation
around the plateau. A single global density overlay is not enough if the
curve shape is central to the question.

## 14. Prediction grid

``` r

grid <- data.frame(
  nitrogen = seq(min(d$nitrogen), max(d$nitrogen), length.out = 101)
)
head(grid)
#>   nitrogen
#> 1      0.0
#> 2      1.8
#> 3      3.6
#> 4      5.4
#> 5      7.2
#> 6      9.0
```

The grid is restricted to the observed domain by default.

## 15. Posterior curve

``` r

pred_m <- bayes_predict(fit_m, grid, type = "mean", re_formula = NA)
head(pred_m$summary)
```

The credible band describes uncertainty in the expected response, not
the scatter of future individual observations. Use `type = "predictive"`
for posterior predictive observations.

## 16. Michaelis-Menten alternative

The Michaelis-Menten template is

``` math
\mu(x)=A\frac{x}{H+x},
```

where $`A`$ is the asymptote and $`H>0`$ is the predictor level at which
the expected response reaches half the asymptote.

``` r

des_mm <- bayes_nonlinear(d, "yield", "nitrogen", model = "michaelis_menten")
bayes_plan(des_mm)
#> <bayes_plan>
#>   Design: Nonlinear Bayesian response model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

## 17. Four-parameter logistic model

A sigmoidal response may be represented by lower and upper asymptotes,
an ED50-like location, and a positive scale parameter.

``` r

des_log <- bayes_nonlinear(d, "yield", "nitrogen", model = "logistic4")
des_log$model_options$nonlinear_model
#> [1] "logistic4"
```

Do not fit a logistic curve solely because it has more flexibility. The
data must contain enough information about both asymptotes and the
transition region.

## 18. Gompertz model

The Gompertz curve is asymmetric around its transition and can be useful
when rise and approach to the upper asymptote are not symmetric.

``` r

des_g <- bayes_nonlinear(d, "yield", "nitrogen", model = "gompertz")
bayes_design_audit(des_g)
#> <bayes_design_audit>
#>   Design: Nonlinear Bayesian response model
#>   Status: PASS
```

## 19. Identifiability before model comparison

If the highest dose is still on the steep part of the curve, the
posterior for the asymptote can be broad and strongly correlated with
the rate. That is a design-information problem, not merely a sampler
problem.

Useful diagnostics include posterior pair plots, prior-to-posterior
contraction, predictive uncertainty near the domain boundary, and
simulation-based parameter recovery.

## 20. Parameter recovery

A new nonlinear application should be validated with data generated from
known parameters. The workflow is:

``` text
choose realistic generating parameters
        |
        v
simulate experimental responses
        |
        v
fit the exact intended model
        |
        v
check posterior coverage and bias
        |
        v
repeat across realistic sample sizes
```

This follows the general Stan practice of validating model
implementations on known simulated truth before relying on real-data
estimates (Annis et al. 2017).

## 21. Comparing nonlinear templates

When several shapes are scientifically plausible, compare them with
posterior predictive checks and out-of-sample predictive criteria.

``` r

loo_m  <- bayes_loo(fit_m)
loo_mm <- bayes_loo(fit_mm)
bayes_compare_models(fit_m, fit_mm)
```

A small ELPD difference relative to its uncertainty does not support a
categorical declaration that one biological mechanism is true.
Predictive comparison and mechanistic interpretation are different
inferential targets.

## 22. Bayes factors are not automatic here

Flexible nonlinear models can be compared by marginal likelihood only
when priors are proper and model comparison is scientifically justified.
Version 1.0.0 therefore does not manufacture a Bayes factor for every
nonlinear choice. Predictive comparison is usually the safer default for
exploratory curve selection.

## 23. Derived optima

For monotone saturation curves, a finite biological maximum may not
exist inside the experimental domain. Consequently,
[`bayes_optimum()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_optimum.md)
should be interpreted according to the curve definition and target. A
grid maximum at the upper boundary is not evidence that the true
biological optimum equals the largest tested dose.

## 24. Scientific thresholds can be more useful than maxima

For saturating curves, questions such as the dose required to achieve
90% or 95% of the asymptote may be more meaningful than a mathematical
maximum. Such derived quantities should be computed from each posterior
draw so uncertainty propagates correctly. They are candidates for a
later decision-oriented extension.

## 25. Sensitivity analysis

Repeat the analysis under priors that are all scientifically plausible.
Focus on whether posterior conclusions about the response curve, derived
thresholds, and predictions materially change. Sensitivity is especially
important when the asymptote is weakly informed by the observed domain.

## 26. Frequentist crosswalk

A frequentist nonlinear least-squares curve can be useful as a teaching
comparator, but its point estimate and approximate confidence interval
do not become a Bayesian posterior. The comparison should emphasize
estimands, assumptions, and uncertainty rather than treating one
framework as a translation table for the other.

## 27. Reporting checklist

A reproducible nonlinear report should state the response distribution,
curve equation, parameter definitions and units, priors, observed
predictor domain, sampler settings, convergence diagnostics, posterior
predictive checks, posterior parameter summaries, prediction intervals,
sensitivity analysis, and the rule used for any derived decision. This
structure follows the transparency principles emphasized by Bayesian
reporting guidelines (Kruschke 2021).

## 28. Complete starter workflow

``` r

d <- bayes_teaching_data("nonlinear")
des <- bayes_nonlinear(d, "yield", "nitrogen", model = "mitscherlich")

ctrl <- bayes_compute(backend = "cmdstanr", n_cpus = 8,
                      chains = 4, parallel_chains = 4)
fit <- bayes_fit(des, engine = "brms", compute = ctrl)

bayes_diagnose(fit)
bayes_pp_check(fit)

grid <- data.frame(nitrogen = seq(min(d$nitrogen), max(d$nitrogen), length.out = 101))
pred <- bayes_predict(fit, grid, type = "mean")
```

## 29. Scope and future extension

Version 1.0.0 supplies four named templates and a stable
design-to-backend architecture. Measurement-error and censoring
workflows are now available through dedicated model specifications.
Future releases can add plateau models, segmented dose-response curves,
nonlinear mixed models with treatment-specific parameters, and richer
combinations of imperfect observation processes. The current 1.0
decision layer can already consume posterior outcome draws for utility
and regret calculations without replacing the common
[`bayes_fit()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_fit.md),
prediction, diagnostic, and reporting interfaces.

## References

The implementation and workflow build on Stan/brms nonlinear modeling
and general Bayesian computation principles (Bürkner 2017; Annis et al.
2017; Kruschke 2021). \# Reproducibility checklist

Before treating the worked analysis as scientific evidence, verify each
item below:

1.  **Check 1.** the experimental unit and prediction target are stated
    explicitly.

    Record the result of this check in the analysis notebook and explain
    any exception.

2.  **Check 2.** the response scale and physical units are documented.

    Record the result of this check in the analysis notebook and explain
    any exception.

3.  **Check 3.** the candidate model was chosen before examining
    inferential summaries when the analysis is confirmatory.

    Record the result of this check in the analysis notebook and explain
    any exception.

4.  **Check 4.** priors are reported together with their scientific
    rationale.

    Record the result of this check in the analysis notebook and explain
    any exception.

5.  **Check 5.** prior predictive simulations cover plausible but not
    absurd agronomic responses.

    Record the result of this check in the analysis notebook and explain
    any exception.

6.  **Check 6.** all sampler diagnostics are inspected rather than only
    R-hat.

    Record the result of this check in the analysis notebook and explain
    any exception.

7.  **Check 7.** posterior predictive checks address features that
    matter for the scientific question.

    Record the result of this check in the analysis notebook and explain
    any exception.

8.  **Check 8.** the interpolation domain is separated from
    extrapolation.

    Record the result of this check in the analysis notebook and explain
    any exception.

9.  **Check 9.** posterior intervals accompany point summaries.

    Record the result of this check in the analysis notebook and explain
    any exception.

10. **Check 10.** practical thresholds and ROPE limits are justified in
    measurement units.

Record the result of this check in the analysis notebook and explain any
exception.

11. **Check 11.** model comparison is interpreted according to its
    target, predictive or evidential.

Record the result of this check in the analysis notebook and explain any
exception.

12. **Check 12.** sensitivity to priors and important tuning choices is
    documented.

Record the result of this check in the analysis notebook and explain any
exception.

13. **Check 13.** the computation seed and backend versions are stored.

Record the result of this check in the analysis notebook and explain any
exception.

14. **Check 14.** the complete code can be run without personal absolute
    paths.

Record the result of this check in the analysis notebook and explain any
exception.

15. **Check 15.** the reported conclusion does not claim more than the
    fitted likelihood and design support.

Record the result of this check in the analysis notebook and explain any
exception.

Annis, Jeffrey, Brent J. Miller, and Thomas J. Palmeri. 2017. “Bayesian
Inference with Stan: A Tutorial on Adding Custom Distributions.”
*Behavior Research Methods* 49: 863–86.
<https://doi.org/10.3758/s13428-016-0746-9>.

Bürkner, Paul-Christian. 2017. “Brms: An r Package for Bayesian
Multilevel Models Using Stan.” *Journal of Statistical Software* 80 (1):
1–28. <https://doi.org/10.18637/jss.v080.i01>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.
