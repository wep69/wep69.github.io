# bayesAgriFlow: From Experimental Design Foundations to Unified Bayesian Analysis

## 1. Why this tutorial exists

Bayesian analysis of agricultural experiments can become difficult for a
reason that is independent of Bayes’ theorem. Several scientific
decisions have to be made at the same time:

1.  identify the experimental unit;
2.  reconstruct the randomization and blocking structure;
3.  decide which effects are fixed, nuisance, or grouping-level;
4.  choose a likelihood;
5.  choose priors and inspect what they imply before observing the data;
6.  fit the model with a numerical method appropriate for its
    complexity;
7.  diagnose the fit before interpreting it;
8.  distinguish parameter estimation from evidence between hypotheses;
9.  define treatment contrasts in scientifically meaningful units;
10. decide whether a difference is merely directional or large enough to
    matter;
11. assess predictive adequacy;
12. communicate the result without translating Bayesian quantities into
    frequentist terminology.

`bayesAgriFlow` is organized around those decisions. Its central rule
is:

**Start with the experimental unit and randomization. Define the
scientific estimand. Specify priors. Fit and diagnose the model. Only
then interpret effects, comparisons, evidence, and decisions.**

This principle follows the design-first instructional structure used
throughout the Scientific Package Builder workflow. It is also
consistent with Bayesian reporting guidance that separates model
specification, prior justification, computation, posterior description,
decisions, sensitivity, and reproducibility (Kruschke 2021; Doorn et al.
2021).

The package does not present a Bayes factor as a Bayesian p-value. A
Bayes factor compares the predictive performance of specified models or
hypotheses, whereas posterior estimation describes uncertainty about
parameters or derived quantities (Tendeiro et al. 2025; Held and Ott
2018).

## 2. Learning objectives

After completing this tutorial, the reader should be able to:

1.  create explicit CRD, RCBD, Latin-square, factorial, and split-plot
    design objects;
2.  audit replication, balance, Latin-square structure, and split-plot
    experimental units;
3.  distinguish a design object from a fitted statistical model;
4.  specify transparent priors and perform prior predictive checks;
5.  use the dependency-light native Gaussian teaching engine;
6.  understand when the `brms`/Stan route is required;
7.  configure `cmdstanr`, parallel chains, CPU budgets, and optional
    within-chain threads;
8.  summarize posterior location, uncertainty, direction probability,
    and standardized descriptive effect indices;
9.  construct pairwise treatment contrasts from posterior draws;
10. use a scientifically justified region of practical equivalence;
11. calculate probabilities of exceeding meaningful effect thresholds;
12. distinguish posterior estimation, ROPE decisions, and Bayes-factor
    evidence;
13. perform posterior predictive checks;
14. interpret R-hat, effective sample sizes, divergences, tree depth,
    and E-BFMI for Stan fits;
15. use the frequentist crosswalk as a teaching aid without treating its
    output as a Bayesian calibration;
16. produce a Markdown report and audit reporting completeness;
17. analyze quantitative factors, polynomial curves, ANCOVA, and
    posterior optima;
18. specify general mixed models with random slopes, nested or crossed
    grouping, and heterogeneous residual variation;
19. model repeated measurements with explicit subject and time roles and
    an optional Gaussian AR(1) residual structure;
20. analyze split-split-plot and strip-plot randomization structures;
21. fit non-Gaussian GLMMs and interpret effects on the response scale;
22. understand how the version 1.0.0 architecture supports classical,
    flexible, complex-system, model-uncertainty, and decision workflows
    and can accommodate future spatial field trials,
    genotype-by-environment models, multivariate responses, measurement
    error, censoring, and other advanced extensions.

## 3. The package in one map

The public API is intentionally layered.

| Layer | Scientific purpose | Main functions |
|----|----|----|
| Design | Encode experimental roles and randomization | [`bayes_design()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_design.md), [`bayes_crd()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_crd.md), [`bayes_rcbd()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_rcbd.md), [`bayes_latin_square()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_latin_square.md), [`bayes_factorial()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_factorial.md), [`bayes_split_plot()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_split_plot.md), [`bayes_split_split_plot()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_split_split_plot.md), [`bayes_strip_plot()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_strip_plot.md), [`bayes_mixed()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_mixed.md), [`bayes_repeated()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_repeated.md), [`bayes_glmm()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_glmm.md) |
| Audit | Detect structural problems before fitting | [`bayes_design_audit()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_design_audit.md), [`bayes_plan()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_plan.md) |
| Priors | Make prior assumptions explicit | [`bayes_prior()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_prior.md), [`bayes_prior_predictive()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_prior_predictive.md), [`bayes_prior_sensitivity()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_prior_sensitivity.md) |
| Computation | Control backend and hardware | [`bayes_compute()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compute.md), [`bayes_compute_info()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compute_info.md), [`bayes_capabilities()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_capabilities.md) |
| Fit | Compile and estimate the model | [`bayes_fit()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_fit.md), [`bayes_analyze()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_analyze.md) |
| Posterior | Estimate effects and contrasts | [`bayes_summary()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_summary.md), [`bayes_effects()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_effects.md), [`bayes_contrasts()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_contrasts.md), [`bayes_hypothesis()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_hypothesis.md) |
| Practical relevance | Define equivalence and meaningful effects | [`bayes_rope()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_rope.md), [`bayes_equivalence()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_equivalence.md), [`bayes_practical_significance()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_practical_significance.md) |
| Evidence | Compare models or hypotheses | [`bayes_evidence()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_evidence.md), [`bayes_bf()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_bf.md), [`bayes_inclusion_bf()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_inclusion_bf.md) |
| Prediction | Obtain response-scale means or predictive distributions | [`bayes_predict()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_predict.md), [`bayes_optimum()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_optimum.md), [`bayes_economic_optimum()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_economic_optimum.md) |
| Hierarchy | Summarize group-level variation and compare models | [`bayes_variance_components()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_variance_components.md), [`bayes_random_effects()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_random_effects.md), [`bayes_loo()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_loo.md), [`bayes_compare_models()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compare_models.md) |
| Validation | Check computation and predictive behavior | [`bayes_diagnose()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_diagnose.md), [`bayes_pp_check()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_pp_check.md) |
| Communication | Compare frameworks and report | [`bayes_compare_frequentist()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compare_frequentist.md), [`bayes_plot()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_plot.md), [`bayes_table()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_table.md), [`bayes_report()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_report.md), [`bayes_report_check()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_report_check.md) |

The conceptual workflow is

``` math
\boxed{
\text{design}
\rightarrow
\text{audit}
\rightarrow
\text{prior}
\rightarrow
\text{fit}
\rightarrow
\text{diagnostics}
\rightarrow
\text{posterior effects}
\rightarrow
\text{practical relevance}
\rightarrow
\text{evidence/prediction}
\rightarrow
\text{report}
}
```

## 4. Teaching data are not field evidence

The package bundles fifteen simulated datasets. Their purpose is to make
examples reproducible and to support parameter-recovery and validation
exercises across simple, quantitative, hierarchical, repeated, and
non-Gaussian models.

``` r

lapply(
  c("crd", "rcbd", "latin_square", "factorial", "split_plot",
    "dose", "ancova", "qual_quant", "mixed", "repeated",
    "split_split", "strip_plot", "glmm_count", "glmm_binomial",
    "heterogeneous"),
  function(x) dim(bayes_teaching_data(x))
)
#> [[1]]
#> [1] 32  3
#> 
#> [[2]]
#> [1] 20  3
#> 
#> [[3]]
#> [1] 25  4
#> 
#> [[4]]
#> [1] 24  4
#> 
#> [[5]]
#> [1] 36  5
#> 
#> [[6]]
#> [1] 30  2
#> 
#> [[7]]
#> [1] 36  3
#> 
#> [[8]]
#> [1] 72  3
#> 
#> [[9]]
#> [1] 40  3
#> 
#> [[10]]
#> [1] 120   5
#> 
#> [[11]]
#> [1] 48  7
#> 
#> [[12]]
#> [1] 36  6
#> 
#> [[13]]
#> [1] 72  5
#> 
#> [[14]]
#> [1] 48  5
#> 
#> [[15]]
#> [1] 32  3
```

The generator seed and design descriptions are stored in
`inst/extdata/TEACHING_DATA_METADATA.json`. Numerical results from these
datasets illustrate software behavior only.

## 5. Begin with a completely randomized design

Consider a treatment experiment in which each experimental unit receives
one of four bioinput treatments.

``` r

d_crd <- bayes_teaching_data("crd")

crd <- bayes_crd(
  data = d_crd,
  response = "yield",
  treatment = "treatment"
)

crd
#> <bayes_design>
#>   Type: Completely randomized design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   treatment: treatment
bayes_design_audit(crd)
#> <bayes_design_audit>
#>   Design: Completely randomized design
#>   Status: PASS
```

The design object does not fit a model. It records the roles that must
be preserved by any backend.

### 5.1 Analysis plan

``` r

bayes_plan(crd)
#> <bayes_plan>
#>   Design: Completely randomized design
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, prediction
bayes_capabilities("crd")
#>   design      engine             status estimation evidence hierarchical
#> 1    crd      native          supported       TRUE    FALSE        FALSE
#> 2    crd bayesfactor supported_optional      FALSE     TRUE        FALSE
#> 3    crd        brms supported_optional       TRUE     TRUE        FALSE
#> 4    crd        pymc      not_supported      FALSE    FALSE        FALSE
#>   non_gaussian flexible_response
#> 1        FALSE             FALSE
#> 2        FALSE             FALSE
#> 3        FALSE             FALSE
#> 4        FALSE             FALSE
#>                                                                         notes
#> 1                                         Conjugate Gaussian teaching engine.
#> 2                                Default-prior Gaussian model-space evidence.
#> 3                   Stan posterior model through brms with cmdstanr or rstan.
#> 4 PyMC is not a general replacement backend for this design in version 1.0.0.
```

For simple fixed-effect Gaussian designs, version 1.0.0 chooses the
native conjugate engine by default. This route is primarily pedagogical
and provides a small dependency footprint. It is not intended to replace
a fully specified Stan model when the scientific analysis requires
custom priors, hierarchical effects, alternative likelihoods, or more
complex parameterization.

## 6. Prior specification before fitting

A Bayesian model contains a likelihood and priors. Priors are not an
optional annotation added after fitting.

``` r

pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

The native teaching engine uses scale-aware regularizing priors. Its
intercept prior is centered at the observed response mean and scale is
calibrated to the observed response SD. That construction is transparent
and convenient for instruction, but it is partly data-calibrated.
Confirmatory studies that require priors elicited independently of the
current data should use explicit scientific priors through the `brms`
path.

### 6.1 Prior predictive checking

``` r

prior_sim <- bayes_prior_predictive(crd, pr, draws = 200)
prior_sim$summary
#>   statistic      value
#> 1   minimum -6.7560590
#> 2    median  6.6994963
#> 3   maximum 17.2510564
#> 4   mean_sd  0.9512184
```

A prior predictive check asks whether draws from the prior model
generate data that are plausible on the measurement scale. It is not an
assessment of model fit to the observed data. Bayesian reporting
guidelines explicitly recommend showing that the prior generates
scientifically defensible data patterns (Kruschke 2021).

## 7. Fit the native posterior model

For the Gaussian fixed-effect engine,

``` math
\mathbf y \mid \boldsymbol\beta,\sigma^2
\sim
N(\mathbf X\boldsymbol\beta,\sigma^2\mathbf I),
```

with

``` math
\boldsymbol\beta \mid \sigma^2
\sim
N(\mathbf m_0,\sigma^2\mathbf V_0),
\qquad
\sigma^2 \sim \operatorname{InvGamma}(a_0,b_0).
```

The posterior remains conjugate. Let

``` math
\mathbf V_n =
(\mathbf V_0^{-1}+\mathbf X^\mathsf T\mathbf X)^{-1}
```

and

``` math
\mathbf m_n =
\mathbf V_n
\left(
\mathbf V_0^{-1}\mathbf m_0+
\mathbf X^\mathsf T\mathbf y
\right).
```

Then posterior draws of $`\sigma^2`$ and $`\boldsymbol\beta`$ can be
obtained directly without an MCMC chain. This is useful for teaching
because sampling diagnostics such as R-hat and divergences are not
needed for this narrow conjugate model.

``` r

fit_crd <- bayes_fit(
  crd,
  engine = "native",
  prior = pr,
  draws = 2000,
  seed = 20260824
)

fit_crd
#> <bayes_fit>
#>   Design: Completely randomized design
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ treatment
#>   Posterior draws: 2000
head(bayes_summary(fit_crd))
#>          parameter       mean     median         sd      lower     upper
#> 1      (Intercept)  6.2570137  6.2536821 0.13483680  5.9882409 6.5248073
#> 2    treatmentBioB  0.4147625  0.4128233 0.19026810  0.0387634 0.7941410
#> 3    treatmentBioC  0.6978173  0.6965699 0.19027229  0.3181097 1.0602335
#> 4 treatmentControl -0.2035016 -0.2019511 0.19307615 -0.5717017 0.1805676
#> 5            sigma  0.3973025  0.3942947 0.04855044  0.3133095 0.5083499
#>   p_positive p_negative     pd
#> 1     1.0000     0.0000 1.0000
#> 2     0.9835     0.0165 0.9835
#> 3     1.0000     0.0000 1.0000
#> 4     0.1390     0.8610 0.8610
#> 5     1.0000     0.0000 1.0000
```

## 8. Effects: direction is not magnitude

``` r

head(bayes_effects(fit_crd))
#>          parameter       mean     median         sd      lower     upper
#> 1      (Intercept)  6.2570137  6.2536821 0.13483680  5.9882409 6.5248073
#> 2    treatmentBioB  0.4147625  0.4128233 0.19026810  0.0387634 0.7941410
#> 3    treatmentBioC  0.6978173  0.6965699 0.19027229  0.3181097 1.0602335
#> 4 treatmentControl -0.2035016 -0.2019511 0.19307615 -0.5717017 0.1805676
#> 5            sigma  0.3973025  0.3942947 0.04855044  0.3133095 0.5083499
#>   p_positive p_negative     pd standardized_median
#> 1     1.0000     0.0000 1.0000          11.6762397
#> 2     0.9835     0.0165 0.9835           0.7707816
#> 3     1.0000     0.0000 1.0000           1.3005646
#> 4     0.1390     0.8610 0.8610          -0.3770625
#> 5     1.0000     0.0000 1.0000                  NA
#>                                                                              effect_index_note
#> 1 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 2 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 3 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 4 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 5                                                   Scale, dispersion, or dependence parameter
```

The output separates several ideas.

- The posterior median is an effect estimate.
- The credible interval describes posterior uncertainty.
- `p_positive` and `p_negative` describe direction.
- `pd` is the larger of those two direction probabilities.
- `standardized_median` is a descriptive scaling by the observed
  response SD.

A high probability that an effect is positive does not establish that
the effect is agronomically important. Direction and practical magnitude
are different questions (Makowski et al. 2019).

## 9. Treatment comparisons

``` r

con_crd <- bayes_contrasts(
  fit_crd,
  factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

con_crd
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4147625 -0.4128233 0.1902681 -0.7941410
#> 2    BioA - BioC    BioA    BioC -0.6978173 -0.6965699 0.1902723 -1.0602335
#> 3 BioA - Control    BioA Control  0.2035016  0.2019511 0.1930762 -0.1805676
#> 4    BioB - BioC    BioB    BioC -0.2830548 -0.2808429 0.2010837 -0.6817292
#> 5 BioB - Control    BioB Control  0.6182641  0.6143176 0.1998941  0.2263979
#> 6 BioC - Control    BioC Control  0.9013189  0.8981137 0.1969673  0.5138723
#>         upper p_a_gt_b p_a_lt_b     pd rope_pct p_abs_gt_meaningful
#> 1 -0.03876340   0.0165   0.9835 0.9835   0.1240              0.5265
#> 2 -0.31810974   0.0000   1.0000 1.0000   0.0065              0.9395
#> 3  0.57170170   0.8610   0.1390 0.8610   0.4795              0.1590
#> 4  0.09978978   0.0815   0.9185 0.9185   0.3260              0.2820
#> 5  1.03074929   0.9975   0.0025 0.9975   0.0170              0.8700
#> 6  1.29147557   1.0000   0.0000 1.0000   0.0000              0.9925
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1          0.0005                0.5260
#> 2          0.0000                0.9395
#> 3          0.1575                0.0015
#> 4          0.0010                0.2810
#> 5          0.8700                0.0000
#> 6          0.9925                0.0000
```

For treatment levels $`i`$ and $`j`$, the package forms posterior draws

``` math
\Delta_{ij}^{(s)} =
\mu_i^{(s)}-\mu_j^{(s)}.
```

It then reports quantities such as

``` math
P(\Delta_{ij}>0\mid y),
```

``` math
P(\Delta_{ij}\in \mathrm{ROPE}\mid y),
```

and

``` math
P(|\Delta_{ij}|>\delta_{\mathrm{meaningful}}\mid y).
```

These are not interchangeable statistics. The first concerns direction,
the second concerns a user-defined equivalence region, and the third
concerns a minimum effect magnitude.

## 10. ROPE must be scientific

A ROPE is not a generic statistical constant. Its limits should
correspond to changes that are negligible for the scientific
application.

``` r

bayes_rope(
  fit_crd,
  range = c(-0.20, 0.20),
  factor = "treatment"
)
#>         contrast     median      lower       upper     pd rope_pct
#> 1    BioA - BioB -0.4128233 -0.7941410 -0.03876340 0.9835   0.1240
#> 2    BioA - BioC -0.6965699 -1.0602335 -0.31810974 1.0000   0.0065
#> 3 BioA - Control  0.2019511 -0.1805676  0.57170170 0.8610   0.4795
#> 4    BioB - BioC -0.2808429 -0.6817292  0.09978978 0.9185   0.3260
#> 5 BioB - Control  0.6143176  0.2263979  1.03074929 0.9975   0.0170
#> 6 BioC - Control  0.8981137  0.5138723  1.29147557 1.0000   0.0000

bayes_equivalence(
  fit_crd,
  range = c(-0.20, 0.20),
  probability = 0.95,
  factor = "treatment"
)
#>         contrast     median      lower       upper     pd rope_pct
#> 1    BioA - BioB -0.4128233 -0.7941410 -0.03876340 0.9835   0.1240
#> 2    BioA - BioC -0.6965699 -1.0602335 -0.31810974 1.0000   0.0065
#> 3 BioA - Control  0.2019511 -0.1805676  0.57170170 0.8610   0.4795
#> 4    BioB - BioC -0.2808429 -0.6817292  0.09978978 0.9185   0.3260
#> 5 BioB - Control  0.6143176  0.2263979  1.03074929 0.9975   0.0170
#> 6 BioC - Control  0.8981137  0.5138723  1.29147557 1.0000   0.0000
#>   required_probability equivalent_by_rule                  decision_rule
#> 1                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 2                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 3                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 4                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 5                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 6                 0.95              FALSE Posterior mass in ROPE >= 0.95
```

BARG recommends reporting and justifying both ROPE limits and the
posterior mass required for a decision (Kruschke 2021). The package
therefore does not silently assign a universal treatment-comparison
ROPE.

## 11. Prior sensitivity

``` r

sens <- bayes_prior_sensitivity(
  crd,
  coefficient_scales = c(1, 2.5, 5),
  draws = 1000
)

head(sens)
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.3250091  6.3282503 0.12815626  6.06903480 6.5718821
#> 2    treatmentBioB  0.3098958  0.3015390 0.17158259 -0.01327856 0.6453971
#> 3    treatmentBioC  0.5729147  0.5738773 0.17865663  0.21950870 0.9077320
#> 4 treatmentControl -0.2362121 -0.2324882 0.17918649 -0.59365578 0.1289241
#> 5            sigma  0.4149200  0.4133279 0.04877378  0.33022612 0.5275931
#> 6      (Intercept)  6.2631784  6.2658471 0.14082987  5.97902781 6.5269269
#>   p_positive p_negative    pd coefficient_scale
#> 1      1.000      0.000 1.000               1.0
#> 2      0.970      0.030 0.970               1.0
#> 3      0.997      0.003 0.997               1.0
#> 4      0.084      0.916 0.916               1.0
#> 5      1.000      0.000 1.000               1.0
#> 6      1.000      0.000 1.000               2.5
```

Sensitivity analysis asks whether conclusions depend strongly on
plausible alternative priors. This becomes especially important when
data are sparse or Bayes factors are used, because marginal likelihoods
depend on priors (Tendeiro et al. 2025; Held and Ott 2018).

## 12. Posterior predictive checking

``` r

ppc <- bayes_pp_check(fit_crd, ndraws = 200)
ppc$observed
#>         mean        sd minimum  maximum      q10      q90
#> 10% 6.484965 0.5355904 5.66591 7.676907 5.855358 7.127354
summary(ppc$replicated$mean)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   6.188   6.411   6.477   6.483   6.561   6.725
summary(ppc$replicated$sd)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>  0.3652  0.4897  0.5473  0.5534  0.6033  0.8365
```

The package returns replicated draws, not only a single figure. This
makes it possible to build checks that correspond to the actual design.
For example, one can compare group-specific spreads, extreme values, or
replicated treatment differences.

## 13. RCBD: randomization changes the model

Load the blocked cultivar experiment.

``` r

d_rcbd <- bayes_teaching_data("rcbd")

rcbd <- bayes_rcbd(
  d_rcbd,
  response = "yield",
  treatment = "cultivar",
  block = "block"
)

bayes_design_audit(rcbd)
#> <bayes_design_audit>
#>   Design: Randomized complete block design
#>   Status: PASS
fit_rcbd <- bayes_fit(rcbd, engine = "native", draws = 2000)
bayes_contrasts(fit_rcbd, "cultivar", meaningful = 0.30)
#>    contrast level_a level_b          mean       median        sd        lower
#> 1   C1 - C2      C1      C2 -0.3897560432 -0.383550254 0.2077938 -0.804235854
#> 2   C1 - C3      C1      C3 -0.8144609728 -0.817134081 0.2028738 -1.220772944
#> 3   C1 - C4      C1      C4 -0.7873035072 -0.786374456 0.2060990 -1.176440615
#> 4   C1 - C5      C1      C5 -0.3894009763 -0.388420072 0.2036008 -0.796014074
#> 5   C2 - C3      C2      C3 -0.4247049296 -0.426589662 0.2097690 -0.838586178
#> 6   C2 - C4      C2      C4 -0.3975474640 -0.402819030 0.2090511 -0.788254262
#> 7   C2 - C5      C2      C5  0.0003550668 -0.003223194 0.2117464 -0.422840882
#> 8   C3 - C4      C3      C4  0.0271574656  0.028499947 0.2114304 -0.386617520
#> 9   C3 - C5      C3      C5  0.4250599964  0.421312364 0.2147351 -0.003661826
#> 10  C4 - C5      C4      C5  0.3979025309  0.398466296 0.2172659 -0.040578393
#>           upper p_a_gt_b p_a_lt_b     pd rope_pct p_abs_gt_meaningful
#> 1   0.009337911   0.0295   0.9705 0.9705       NA              0.6715
#> 2  -0.417001013   0.0000   1.0000 1.0000       NA              0.9925
#> 3  -0.375693536   0.0000   1.0000 1.0000       NA              0.9870
#> 4   0.014961946   0.0300   0.9700 0.9700       NA              0.6790
#> 5  -0.003302769   0.0235   0.9765 0.9765       NA              0.7265
#> 6   0.011741682   0.0280   0.9720 0.9720       NA              0.6965
#> 7   0.434135627   0.4940   0.5060 0.5060       NA              0.1550
#> 8   0.436462849   0.5515   0.4485 0.5515       NA              0.1600
#> 9   0.867696037   0.9735   0.0265 0.9735       NA              0.7330
#> 10  0.811663357   0.9660   0.0340 0.9660       NA              0.6890
#>    p_gt_meaningful p_lt_minus_meaningful
#> 1           0.0015                0.6700
#> 2           0.0000                0.9925
#> 3           0.0000                0.9870
#> 4           0.0000                0.6790
#> 5           0.0005                0.7260
#> 6           0.0015                0.6950
#> 7           0.0760                0.0790
#> 8           0.1020                0.0580
#> 9           0.7320                0.0010
#> 10          0.6870                0.0020
```

In the native engine, blocks enter as nuisance fixed effects. In the
`brms` hierarchical compiler, RCBD blocks enter as a grouping-level
intercept. In the `BayesFactor` evidence adapter they may also be
represented as random nuisance terms. Those routes answer closely
related experimental questions, but their priors and parameterizations
are not assumed to be identical.

## 14. Latin square: two blocking directions

``` r

d_lat <- bayes_teaching_data("latin_square")

latin <- bayes_latin_square(
  d_lat,
  response = "biomass",
  treatment = "treatment",
  row = "row",
  column = "column"
)

bayes_design_audit(latin)
#> <bayes_design_audit>
#>   Design: Latin square design
#>   Status: PASS
fit_lat <- bayes_fit(latin, draws = 1500)
bayes_contrasts(fit_lat, "treatment")
#>    contrast level_a level_b        mean      median        sd      lower
#> 1   T1 - T2      T1      T2  0.07628235  0.07351988 0.1558811 -0.2262423
#> 2   T1 - T3      T1      T3 -0.41978738 -0.42429471 0.1477114 -0.7145759
#> 3   T1 - T4      T1      T4 -0.53006373 -0.52753475 0.1547075 -0.8309848
#> 4   T1 - T5      T1      T5 -0.87770567 -0.87388111 0.1470562 -1.1725161
#> 5   T2 - T3      T2      T3 -0.49606973 -0.49372608 0.1575683 -0.8073914
#> 6   T2 - T4      T2      T4 -0.60634608 -0.60626181 0.1604171 -0.9410483
#> 7   T2 - T5      T2      T5 -0.95398801 -0.95274826 0.1597083 -1.2613507
#> 8   T3 - T4      T3      T4 -0.11027635 -0.11156404 0.1566320 -0.4156362
#> 9   T3 - T5      T3      T5 -0.45791828 -0.45663557 0.1504697 -0.7622822
#> 10  T4 - T5      T4      T5 -0.34764194 -0.34998616 0.1580458 -0.6487986
#>          upper     p_a_gt_b  p_a_lt_b        pd rope_pct p_abs_gt_meaningful
#> 1   0.37310353 0.6880000000 0.3120000 0.6880000       NA                  NA
#> 2  -0.13598264 0.0020000000 0.9980000 0.9980000       NA                  NA
#> 3  -0.23526072 0.0000000000 1.0000000 1.0000000       NA                  NA
#> 4  -0.60065095 0.0000000000 1.0000000 1.0000000       NA                  NA
#> 5  -0.17619065 0.0006666667 0.9993333 0.9993333       NA                  NA
#> 6  -0.28489642 0.0000000000 1.0000000 1.0000000       NA                  NA
#> 7  -0.63145947 0.0000000000 1.0000000 1.0000000       NA                  NA
#> 8   0.19859754 0.2406666667 0.7593333 0.7593333       NA                  NA
#> 9  -0.17377323 0.0020000000 0.9980000 0.9980000       NA                  NA
#> 10 -0.03786015 0.0146666667 0.9853333 0.9853333       NA                  NA
#>    p_gt_meaningful p_lt_minus_meaningful
#> 1               NA                    NA
#> 2               NA                    NA
#> 3               NA                    NA
#> 4               NA                    NA
#> 5               NA                    NA
#> 6               NA                    NA
#> 7               NA                    NA
#> 8               NA                    NA
#> 9               NA                    NA
#> 10              NA                    NA
```

The audit verifies that each treatment occurs once per row and once per
column and that each row-column cell contains one observation. The model
does not make a malformed Latin square valid.

## 15. Factorial experiments: interactions first

``` r

d_fac <- bayes_teaching_data("factorial")

fac <- bayes_factorial(
  d_fac,
  response = "yield",
  factors = c("cultivar", "fertilizer"),
  block = "block"
)

bayes_design_audit(fac)
#> <bayes_design_audit>
#>   Design: Qualitative factorial design
#>   Status: PASS
fit_fac <- bayes_fit(fac, draws = 2000)
head(bayes_summary(fit_fac))
#>      parameter       mean     median        sd       lower     upper p_positive
#> 1  (Intercept) 6.78898012 6.78694755 0.1743900  6.44300203 7.1278871     1.0000
#> 2    cultivarB 0.44329366 0.44041515 0.2042175  0.04648123 0.8651731     0.9850
#> 3 fertilizerF1 0.50730820 0.50866762 0.2006515  0.11821673 0.9132704     0.9925
#> 4 fertilizerF2 0.69506465 0.69855773 0.2025902  0.29680424 1.0911078     0.9990
#> 5      blockB2 0.04005412 0.03952958 0.1745235 -0.29006244 0.3826430     0.5950
#> 6      blockB3 0.09656162 0.08565605 0.1715825 -0.22437124 0.4433780     0.7150
#>   p_negative     pd
#> 1     0.0000 1.0000
#> 2     0.0150 0.9850
#> 3     0.0075 0.9925
#> 4     0.0010 0.9990
#> 5     0.4050 0.5950
#> 6     0.2850 0.7150
```

The compiled model contains `cultivar * fertilizer`. When an interaction
is scientifically meaningful, main-effect summaries should not be
interpreted in isolation. Version 1.0.0 allows marginal pairwise
comparisons for one factor at a time, averaged over the observed
distribution of the other factor.

``` r

bayes_contrasts(fit_fac, factor = "cultivar")
#>   contrast level_a level_b      mean     median        sd      lower     upper
#> 1    A - B       A       B -0.703376 -0.7031793 0.1249454 -0.9505347 -0.454503
#>   p_a_gt_b p_a_lt_b pd rope_pct p_abs_gt_meaningful p_gt_meaningful
#> 1        0        1  1       NA                  NA              NA
#>   p_lt_minus_meaningful
#> 1                    NA
bayes_contrasts(fit_fac, factor = "fertilizer")
#>   contrast level_a level_b       mean     median        sd      lower
#> 1  F0 - F1      F0      F1 -0.5725717 -0.5728014 0.1529899 -0.8825749
#> 2  F0 - F2      F0      F2 -1.0199246 -1.0219825 0.1538425 -1.3197477
#> 3  F1 - F2      F1      F2 -0.4473528 -0.4489681 0.1554915 -0.7521343
#>        upper p_a_gt_b p_a_lt_b    pd rope_pct p_abs_gt_meaningful
#> 1 -0.2732464    0.000    1.000 1.000       NA                  NA
#> 2 -0.7194255    0.000    1.000 1.000       NA                  NA
#> 3 -0.1382217    0.006    0.994 0.994       NA                  NA
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1              NA                    NA
#> 2              NA                    NA
#> 3              NA                    NA
```

Dedicated simple-effect and interaction-contrast syntax is reserved for
a later version so that it can be added without changing the core object
model.

## 16. Bayes factors as relative evidence

If `BayesFactor` is installed, a model-space evidence analysis can be
requested.

``` r

fit_bf <- bayes_fit(
  crd,
  engine = "bayesfactor"
)

bayes_evidence(fit_bf)
bayes_inclusion_bf(fit_bf)
```

The package keeps three warnings explicit.

1.  A Bayes factor is not a posterior effect size.
2.  A Bayes factor is not posterior odds unless prior odds are supplied
    and the update is performed.
3.  Evidence from a `BayesFactor` adapter uses that backend’s priors. It
    should not be described as if it came from the native
    posterior-estimation prior.

For $`H_1`$ versus $`H_0`$,

``` math
\frac{P(H_1\mid y)}{P(H_0\mid y)}
=
BF_{10}
\frac{P(H_1)}{P(H_0)}.
```

This updating relationship is central to correct interpretation
(Tendeiro et al. 2025).

## 17. Split plots require hierarchy

The split-plot example contains irrigation as the whole-plot factor and
cultivar as the subplot factor.

``` r

d_sp <- bayes_teaching_data("split_plot")

sp <- bayes_split_plot(
  d_sp,
  response = "yield",
  block = "block",
  whole_plot = "irrigation",
  subplot = "cultivar",
  whole_plot_id = "whole_plot_id"
)

sp
#> <bayes_design>
#>   Type: Split-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   block: block
#>   whole_plot: irrigation
#>   subplot: cultivar
#>   whole_plot_id: whole_plot_id
bayes_design_audit(sp)
#> <bayes_design_audit>
#>   Design: Split-plot design
#>   Status: PASS
bayes_plan(sp)
#> <bayes_plan>
#>   Design: Split-plot design
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

Version 1.0.0 compiles the Gaussian hierarchical model conceptually as

``` math
y \sim A*B + (1\mid block) + (1\mid whole\_plot\_id).
```

The explicit whole-plot identifier is important because
pseudoreplication can occur if subplot observations are treated as
independent replicates of the whole-plot treatment.

## 18. CmdStanR and CPU control

``` r

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 1000,
  seed = 20260824
)

cmp
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
bayes_compute_info(cmp)
#> $compute
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
#> 
#> $brms
#> $brms$installed
#> [1] TRUE
#> 
#> $brms$version
#> [1] "2.23.0"
#> 
#> 
#> $cmdstanr
#> $cmdstanr$installed
#> [1] TRUE
#> 
#> $cmdstanr$version
#> [1] "0.9.0"
#> 
#> 
#> $rstan
#> $rstan$installed
#> [1] TRUE
#> 
#> $rstan$version
#> [1] "2.32.7"
#> 
#> 
#> $BayesFactor
#> $BayesFactor$installed
#> [1] TRUE
#> 
#> $BayesFactor$version
#> [1] "0.9.12.4.8"
#> 
#> 
#> $posterior
#> $posterior$installed
#> [1] TRUE
#> 
#> $posterior$version
#> [1] "1.7.0"
#> 
#> 
#> $reticulate
#> $reticulate$installed
#> [1] TRUE
#> 
#> $reticulate$version
#> [1] "1.46.0"
#> 
#> 
#> $pymc
#> $pymc$available
#> [1] FALSE
#> 
#> $pymc$reticulate
#> [1] TRUE
#> 
#> $pymc$python
#> [1] "H:/uv/AppDataLocalUv/cache/archive-v0/MfuOKTFtveE-Nd3l_RFiM/Scripts/python.exe"
#> 
#> $pymc$python_version
#> [1] "3.12.10 (tags/v3.12.10:0cc8128, Apr  8 2025, 12:21:36) [MSC v.1943 64 bit (AMD64)]"
#> 
#> $pymc$pymc
#> [1] FALSE
#> 
#> $pymc$pymc_version
#> [1] NA
#> 
#> $pymc$pymc_bart
#> [1] FALSE
#> 
#> $pymc$pymc_bart_version
#> [1] NA
#> 
#> $pymc$arviz
#> [1] FALSE
#> 
#> $pymc$arviz_version
#> [1] NA
#> 
#> $pymc$numpy
#> [1] TRUE
#> 
#> $pymc$numpy_version
#> [1] "2.5.2"
#> 
#> 
#> $cmdstan_version
#> [1] "2.37.0"
#> 
#> $cmdstan_path
#> [1] "C:/Users/wep69/.cmdstan/cmdstan-2.37.0"
```

The CPU budget is explicit. If `parallel_chains = 4` and
`threads_per_chain = 2`, the maximum requested workers are eight. The
package rejects configurations that exceed `n_cpus`.

Within-chain threading is optional because it is not universally
beneficial. Current `brms` documentation describes its threading path as
experimental and based on Stan `reduce_sum`; users should benchmark it
on genuinely expensive models rather than assuming that more threads
always reduce wall time.

## 19. Fit a split plot with brms and CmdStan

The full computation is deliberately not evaluated during vignette
building.

``` r

fit_sp <- bayes_fit(
  sp,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = cmp
)

bayes_diagnose(fit_sp)
bayes_contrasts(fit_sp, "irrigation", rope = c(-0.20, 0.20))
bayes_contrasts(fit_sp, "cultivar", meaningful = 0.30)
bayes_pp_check(fit_sp)
```

Heavy Stan analyses belong in the developer-side validation workflow.
Their seeds, versions, CPU configuration, and result hashes should be
frozen before a release vignette uses precomputed results.

## 20. MCMC diagnostics are a gate

For a Stan fit,
[`bayes_diagnose()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_diagnose.md)
summarizes:

- R-hat;
- bulk effective sample size;
- tail effective sample size;
- Monte Carlo standard error;
- divergent transitions;
- maximum tree-depth saturation;
- E-BFMI when energy diagnostics are available.

Stan makes flexible Bayesian models practical, but model flexibility
does not remove the need to establish that the numerical sampler
explored the intended posterior (Annis et al. 2017).

Do not interpret a complex treatment comparison merely because a fitted
object was returned.

## 21. Frequentist comparison is pedagogical

``` r

freq <- bayes_compare_frequentist(fit_crd)
freq$anova
#> Analysis of Variance Table
#> 
#> Response: yield
#>           Df Sum Sq Mean Sq F value    Pr(>F)    
#> treatment  3 4.1604 1.38681  8.2057 0.0004488 ***
#> Residuals 28 4.7321 0.16901                      
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

The package intentionally does not calculate a conversion between a
p-value and a Bayes factor. The two frameworks condition on different
objects and answer different questions. The classical analysis is
included to help a student map familiar experimental-design terminology
onto the Bayesian model, similar to the explicitly pedagogical
frequentist comparison in Tendeiro et al. (2025).

## 22. Reporting

``` r

report_lines <- bayes_report(
  fit_crd,
  contrast_factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

head(report_lines, 20)
#>  [1] "# bayesAgriFlow analysis report"                                                                                                                                                               
#>  [2] ""                                                                                                                                                                                              
#>  [3] "## Scientific and computational scope"                                                                                                                                                         
#>  [4] ""                                                                                                                                                                                              
#>  [5] "- Design: Completely randomized design"                                                                                                                                                        
#>  [6] "- Response(s): `yield`"                                                                                                                                                                        
#>  [7] "- Family: Gaussian"                                                                                                                                                                            
#>  [8] "- Engine: `native`"                                                                                                                                                                            
#>  [9] "- Model: `yield ~ treatment`"                                                                                                                                                                  
#> [10] "- Diagnostic status: **PASS**"                                                                                                                                                                 
#> [11] ""                                                                                                                                                                                              
#> [12] "This report separates posterior estimation, practical relevance, and model evidence. A Bayes factor, when requested, is relative model evidence under stated priors and is not an effect size."
#> [13] ""                                                                                                                                                                                              
#> [14] "## Prior specification"                                                                                                                                                                        
#> [15] ""                                                                                                                                                                                              
#> [16] "- Strategy: regularizing"                                                                                                                                                                      
#> [17] "- Coefficient scale: 2.5"                                                                                                                                                                      
#> [18] "- Intercept scale: 5"                                                                                                                                                                          
#> [19] ""                                                                                                                                                                                              
#> [20] "## Posterior summary"
```

A report audit makes missing components visible.

``` r

bayes_report_check(
  fit_crd,
  rope_supplied = TRUE,
  sensitivity_done = TRUE,
  ppc_done = TRUE
)
#>                                       item status
#> 1           Design and variables specified   TRUE
#> 2        Likelihood/model formula recorded   TRUE
#> 3                           Prior recorded   TRUE
#> 4             Computation/backend recorded   TRUE
#> 5             Posterior interval available   TRUE
#> 6   Sampler/numerical diagnostics reviewed   TRUE
#> 7      Posterior predictive check reviewed   TRUE
#> 8               Prior sensitivity reviewed   TRUE
#> 9  ROPE justified when used for a decision   TRUE
#> 10                           Seed recorded   TRUE
```

This does not guarantee that the science is correct. It checks whether
core reporting components are present.

## 23. Current and future-compatible roles

Version 1.0.0 now uses explicit roles for subject, time, random slopes,
nested and crossed grouping factors, split-split and strip-plot
experimental units, residual-variance groups, binomial trials, and count
exposure. Quantitative factors, ANCOVA, polynomial regression, and
biological or economic posterior optima are also implemented.

The role registry remains intentionally broader than the current
release. Recognized future directions include:

- nonlinear and plateau dose-response models;
- richer repeated-measures covariance structures;
- spatial field trials and spatiotemporal dependence;
- genotype-by-environment hierarchical models;
- multivariate responses;
- measurement error and censoring;
- joint missing-data models;
- ordinal, mixture, hurdle, and zero-inflated responses;
- broader distributional regression;
- Bayesian model averaging;
- projection predictive selection;
- expected utility, regret, and value-of-information decision analysis.

Unimplemented roles are never silently treated as if they had been
modeled. The purpose of the registry is to keep future additions
compatible with the same design-first grammar.

## 24. Quantitative factors, ANCOVA, and Bayesian optima

Version 1.0.0 retains the quantitative workflows introduced after the
initial experimental-design core. A numeric dose is kept numeric when
the scientific question concerns a response curve, whereas a qualitative
treatment remains a factor when the question concerns distinct treatment
levels.

``` r

dose <- bayes_teaching_data("dose")
poly <- bayes_polynomial(dose, "yield", "nitrogen", degree = 2)
bayes_design_audit(poly)
#> <bayes_design_audit>
#>   Design: Polynomial regression
#>   Status: PASS
```

Posterior biological and economic optima are derived from posterior
curve draws rather than from a single fitted coefficient vector.
Boundary probability and the experimental domain remain part of the
interpretation.

## 25. Mixed models, repeated measures, and non-Gaussian responses

The same design-first grammar extends to random slopes, nested and
crossed grouping factors, repeated measurements, split-split plots,
strip plots, and generalized linear mixed models.

``` r

mix <- bayes_mixed(
  bayes_teaching_data("mixed"),
  response = "yield",
  fixed = ~ nitrogen,
  group = "block",
  random_slopes = list(block = "nitrogen")
)
bayes_plan(mix)
#> <bayes_plan>
#>   Design: General mixed-effects model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

The important continuity is that hierarchy is specified from the
experimental structure before fitting rather than reconstructed from a
preferred inferential result.

## 26. Nonlinear curves

Version 1.0.0 adds named nonlinear templates for agronomic response
modeling.

``` r

nl <- bayes_nonlinear(
  bayes_teaching_data("nonlinear"),
  "yield", "nitrogen",
  model = "mitscherlich"
)
bayes_plan(nl)
#> <bayes_plan>
#>   Design: Nonlinear Bayesian response model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

The fitted parameter names have scientific meaning, so priors and
parameter recovery deserve special attention. Use the dedicated
nonlinear vignette before applying a template to publication data.

## 27. Splines and Gaussian processes

When the response shape is unknown but smoothness is plausible, the
package can construct a Bayesian spline or Gaussian process and route it
to `brms`/Stan.

``` r

sm <- bayes_smooth(
  bayes_teaching_data("smooth"),
  "biomass", "soil_moisture", k = 10
)

gp <- bayes_gp(
  bayes_teaching_data("gp"),
  "yield", c("nitrogen", "water"),
  cov = "matern32"
)

bayes_plan(sm)
#> <bayes_plan>
#>   Design: Bayesian smoothing-spline model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
bayes_plan(gp)
#> <bayes_plan>
#>   Design: Gaussian-process regression model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

A flexible curve is not automatically a biological mechanism. Posterior
prediction, uncertainty, support of the observed predictor domain, and
predictive diagnostics remain central.

## 28. Bayesian bootstrap

The Bayesian bootstrap supplies a dependency-light flexible sensitivity
tool for simple exchangeable observations.

``` r

bb <- bayes_bootstrap(
  bayes_teaching_data("crd"),
  "yield", "treatment",
  draws = 1000
)
bayes_bootstrap_contrasts(bb, rope = c(-0.2, 0.2), meaningful = 0.5)
#>         contrast     median      lower      upper    pd rope_pct
#> 1    BioA - BioB -0.4502109 -0.7231193 -0.1496068 0.997    0.044
#> 2    BioA - BioC -0.7359193 -1.1476870 -0.3053474 0.997    0.006
#> 3 BioA - Control  0.1952310 -0.1101607  0.5166632 0.890    0.502
#> 4    BioB - BioC -0.2939353 -0.6621391  0.1308303 0.921    0.310
#> 5 BioB - Control  0.6435451  0.3578355  0.9038254 1.000    0.000
#> 6 BioC - Control  0.9388668  0.4848875  1.3211711 1.000    0.001
#>   p_abs_gt_meaningful
#> 1               0.348
#> 2               0.859
#> 3               0.034
#> 4               0.144
#> 5               0.846
#> 6               0.974
```

It is not presented as a generic substitute for blocked,
repeated-measures, or hierarchical models.

## 29. Optional PyMC-BART

PyMC is optional and is never imported or installed during normal R
package loading. Its first supported role is Bayesian additive
regression trees.

``` r

bd <- bayes_bart(
  bayes_teaching_data("bart"),
  "yield",
  c("nitrogen", "water", "soil_p", "cultivar")
)
bayes_plan(bd)
#> <bayes_plan>
#>   Design: Bayesian additive regression trees
#>   Audit: PASS
#>   Engine: pymc
#>   Reason: Bayesian additive regression trees are implemented through the optional PyMC/PyMC-BART backend in version 1.0.0.
#>   Goals: estimation, prediction
bayes_pymc_info()
#> $available
#> [1] FALSE
#> 
#> $reticulate
#> [1] TRUE
#> 
#> $python
#> [1] "H:/uv/AppDataLocalUv/cache/archive-v0/MfuOKTFtveE-Nd3l_RFiM/Scripts/python.exe"
#> 
#> $python_version
#> [1] "3.12.10 (tags/v3.12.10:0cc8128, Apr  8 2025, 12:21:36) [MSC v.1943 64 bit (AMD64)]"
#> 
#> $pymc
#> [1] FALSE
#> 
#> $pymc_version
#> [1] NA
#> 
#> $pymc_bart
#> [1] FALSE
#> 
#> $pymc_bart_version
#> [1] NA
#> 
#> $arviz
#> [1] FALSE
#> 
#> $arviz_version
#> [1] NA
#> 
#> $numpy
#> [1] TRUE
#> 
#> $numpy_version
#> [1] "2.5.2"
```

BART is most attractive when prediction over unknown nonlinearities and
interactions is more important than estimating a named mechanistic
parameter.

## 30. A complete starter script

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("rcbd")

des <- bayes_rcbd(
  d,
  response = "yield",
  treatment = "cultivar",
  block = "block"
)

audit <- bayes_design_audit(des)
stopifnot(audit$status == "PASS")

prior <- bayes_prior("regularizing")
bayes_prior_predictive(des, prior, draws = 500)

fit <- bayes_fit(
  des,
  engine = "native",
  prior = prior,
  draws = 4000,
  seed = 20260824
)

bayes_diagnose(fit)
bayes_effects(fit)

contrasts <- bayes_contrasts(
  fit,
  factor = "cultivar",
  rope = c(-0.20, 0.20),
  meaningful = 0.30,
  keep_draws = TRUE
)

bayes_pp_check(fit, ndraws = 500)

bayes_report(
  fit,
  file = "analysis.md",
  contrast_factor = "cultivar",
  rope = c(-0.20, 0.20),
  meaningful = 0.30
)
```

## 31. Final checklist

Before reporting a Bayesian agricultural experiment, ask:

Is the experimental unit explicit?

Is randomization encoded rather than inferred from column names?

Did the design audit pass?

Is the likelihood appropriate for the response?

Are priors stated and scientifically interpretable?

Was the prior predictive distribution inspected?

Was the computational backend recorded?

Were CPU, chains, seeds, and versions recorded?

Were sampler diagnostics reviewed when MCMC was used?

Was posterior predictive behavior examined?

Are effect direction and effect magnitude reported separately?

Are ROPE limits or meaningful thresholds scientifically justified?

If a BF is reported, are both competing hypotheses/models and priors
clear?

Was prior sensitivity assessed where it could affect the conclusion?

Is the frequentist output clearly identified as a pedagogical crosswalk?

Can another analyst reproduce the full analysis?

## References

### What version 1.0.0 adds

The current release adds spatial field-trial adjustment, hierarchical
genotype-by-environment models, multivariate responses, explicit
predictor measurement error, one-step continuous missing-data models,
censored responses, ordinal outcomes, finite mixtures, and
distributional regression. These are current capabilities, not future
placeholders. See
[v18-spatial-field-trials.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v18-spatial-field-trials.Rmd),
[v19-genotype-by-environment.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v19-genotype-by-environment.Rmd),
[v20-multivariate-responses.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v20-multivariate-responses.Rmd),
[v21-measurement-error-and-missing-data.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v21-measurement-error-and-missing-data.Rmd),
[v22-censored-responses.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v22-censored-responses.Rmd),
[v23-ordinal-outcomes.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v23-ordinal-outcomes.Rmd),
[v24-finite-mixture-models.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v24-finite-mixture-models.Rmd),
and
[v25-distributional-regression.Rmd](https://wep69.github.io/bayesAgriFlow/articles/v25-distributional-regression.Rmd).

### Version 1.0.0: model uncertainty and decisions

The 1.0.0 layer separates four questions that are often mixed together:
which candidate model receives posterior probability, which models
should contribute to an ensemble prediction, which smaller model can
reproduce a rich reference model, and which action should be selected
after uncertainty is translated into utility. Formal Bayesian model
averaging is discussed in
[v26](https://wep69.github.io/bayesAgriFlow/articles/v26-model-uncertainty-and-predictive-averaging.Rmd)
alongside predictive stacking (Hoeting et al. 1999; Yao et al. 2018).
Projection predictive reduction is developed in
[v27](https://wep69.github.io/bayesAgriFlow/articles/v27-projection-predictive-selection.Rmd)
(Piironen and Vehtari 2017; Pavone et al. 2023).

``` r

set.seed(20260824)
outcomes <- cbind(A = rnorm(2000, 5.0, .4), B = rnorm(2000, 5.5, .6))
u <- bayes_utility(outcomes, value_per_unit = 1000, action_costs = c(A = 0, B = 350))
d <- bayes_decision(u)
d$table
#>   action expected_utility probability_optimal expected_regret
#> B      B         5121.341              0.5685        223.6979
#> A      A         4998.370              0.4315        346.6689
bayes_value_of_information(u)
#>   current_expected_utility expected_utility_with_perfect_information     evpi
#> 1                 5121.341                                  5345.039 223.6979
#>   information_cost net_evpi
#> 1                0 223.6979
```

Posterior utility and expected regret are developed in
[v28](https://wep69.github.io/bayesAgriFlow/articles/v28-bayesian-decision-analysis-utility-and-regret.Rmd).
EVPI and the explicit nested-simulation EVSI contract are developed in
[v29](https://wep69.github.io/bayesAgriFlow/articles/v29-value-of-information-and-research-decisions.Rmd)
(Frazier and Powell 2010; Ades et al. 2004). These functions do not turn
a posterior distribution into an automatic recommendation: actions,
costs, values, and utility assumptions are part of the scientific
decision model and must be reported.

Ades, A. E., G. Lu, and K. Claxton. 2004. “Expected Value of Sample
Information Calculations in Medical Decision Modeling.” *Medical
Decision Making* 24 (2): 207–27.
<https://doi.org/10.1177/0272989X04263162>.

Annis, Jeffrey, Brent J. Miller, and Thomas J. Palmeri. 2017. “Bayesian
Inference with Stan: A Tutorial on Adding Custom Distributions.”
*Behavior Research Methods* 49: 863–86.
<https://doi.org/10.3758/s13428-016-0746-9>.

Doorn, Johnny van, Don van den Bergh, Udo Böhm, et al. 2021. “The JASP
Guidelines for Conducting and Reporting a Bayesian Analysis.”
*Psychonomic Bulletin & Review* 28: 813–26.
<https://doi.org/10.3758/s13423-020-01798-5>.

Frazier, Peter I., and Warren B. Powell. 2010. “Paradoxes in Learning
and the Marginal Value of Information.” *Decision Analysis* 7 (4):
378–403. <https://doi.org/10.1287/deca.1100.0190>.

Held, Leonhard, and Manuela Ott. 2018. “On p-Values and Bayes Factors.”
*Annual Review of Statistics and Its Application* 5: 393–419.
<https://doi.org/10.1146/annurev-statistics-031017-100307>.

Hoeting, Jennifer A., David Madigan, Adrian E. Raftery, and Chris T.
Volinsky. 1999. “Bayesian Model Averaging: A Tutorial.” *Statistical
Science* 14 (4): 382–417. <https://doi.org/10.1214/ss/1009212519>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Makowski, Dominique, Mattan S. Ben-Shachar, S. H. Annabel Chen, and
Daniel Lüdecke. 2019. “Indices of Effect Existence and Significance in
the Bayesian Framework.” *Frontiers in Psychology* 10: 2767.
<https://doi.org/10.3389/fpsyg.2019.02767>.

Pavone, Federico, Juho Piironen, Paul-Christian B "urkner, and Aki
Vehtari. 2023. “Using Reference Models in Variable Selection.”
*Computational Statistics* 38 (1): 349–71.
<https://doi.org/10.1007/s00180-022-01231-6>.

Piironen, Juho, and Aki Vehtari. 2017. “Comparison of Bayesian
Predictive Methods for Model Selection.” *Statistics and Computing* 27
(3): 711–35. <https://doi.org/10.1007/s11222-016-9649-y>.

Tendeiro, Jorge N., Rink Hoekstra, Tsz Keung Wong, and Henk A. L. Kiers.
2025. “Introduction to the Bayes Factor: A Shiny/r App.” *Teaching
Statistics* 47 (1): 5–16. <https://doi.org/10.1111/test.12380>.

Yao, Yuling, Aki Vehtari, Daniel Simpson, and Andrew Gelman. 2018.
“Using Stacking to Average Bayesian Predictive Distributions.” *Bayesian
Analysis* 13 (3): 917–1007. <https://doi.org/10.1214/17-BA1091>.
