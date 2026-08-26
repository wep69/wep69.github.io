# Bayesian Bootstrap and Flexible Nonparametric Inference

## 1. Why a Bayesian bootstrap is included

The Bayesian bootstrap provides a posterior distribution over
empirical-support weights rather than imposing a parametric likelihood
for the observed values. Rubin introduced the method as a Bayesian
analogue of resampling-based reasoning (Rubin 1981). In `bayesAgriFlow`,
it is deliberately presented as a transparent sensitivity and
flexible-estimation tool, not as a universal Bayesian replacement for
Kruskal-Wallis, rank tests, or ANOVA.

## 2. Learning objectives

This vignette develops Dirichlet posterior weights, posterior means and
medians, grouped summaries, pairwise contrasts, probability of
direction, ROPE, meaningful-effect probabilities, reproducibility,
small-sample behavior, and the limits of what the Bayesian bootstrap can
answer.

## 3. The posterior-weight idea

For observations $`y_1,\ldots,y_n`$, the Bayesian bootstrap assigns
posterior random weights

``` math
(w_1,\ldots,w_n)\sim\text{Dirichlet}(1,\ldots,1),
```

and computes a functional of the weighted empirical distribution. A
posterior mean draw is

``` math
\theta^{(s)}=\sum_i w_i^{(s)}y_i.
```

The package simulates the Dirichlet weights using normalized independent
exponential draws.

## 4. Frozen CRD data

``` r

d <- bayes_teaching_data("crd")
head(d)
#>   treatment replicate    yield
#> 1   Control         1 6.388969
#> 2   Control         2 6.146232
#> 3   Control         3 5.854985
#> 4   Control         4 6.708967
#> 5   Control         5 5.931464
#> 6   Control         6 5.665910
```

## 5. Overall posterior mean

``` r

bb_all <- bayes_bootstrap(d, "yield", draws = 2000, seed = 20260824)
bb_all
#> <bayes_bootstrap>
#>   Statistic: mean
#>   Draws: 2000
#>    group     mean  median         sd    lower   upper
#>  overall 6.485182 6.48101 0.09243863 6.312948 6.66928
```

The posterior interval describes uncertainty in the weighted empirical
functional under the Bayesian-bootstrap model.

## 6. Group-specific posterior means

``` r

bb <- bayes_bootstrap(
  d, response = "yield", group = "treatment",
  statistic = "mean", draws = 2000, seed = 20260824
)
bb$summary
#>     group     mean   median         sd    lower    upper
#> 1    BioA 6.241161 6.237753 0.11167958 6.029459 6.470462
#> 2    BioB 6.683318 6.679354 0.08463062 6.531233 6.860030
#> 3    BioC 6.969084 6.973818 0.18276113 6.597724 7.318655
#> 4 Control 6.049921 6.034761 0.10950683 5.869054 6.296387
```

## 7. Posterior medians

``` r

bb_med <- bayes_bootstrap(d, "yield", "treatment", statistic = "median", draws = 1500)
bb_med$summary
#>     group     mean   median        sd    lower    upper
#> 1    BioA 6.215271 6.253010 0.1789150 5.858721 6.600682
#> 2    BioB 6.632807 6.506384 0.1675513 6.447090 6.982219
#> 3    BioC 7.009940 7.031929 0.3152139 6.494981 7.647230
#> 4 Control 5.965560 5.889256 0.1611167 5.799554 6.388969
```

Posterior medians can be useful for skewed or outlier-prone responses,
but changing the estimand from mean to median is a scientific decision,
not a device for obtaining a preferred conclusion.

## 8. Pairwise posterior contrasts

``` r

con <- bayes_bootstrap_contrasts(bb)
con
#>         contrast     median      lower      upper     pd rope_pct
#> 1    BioA - BioB -0.4421089 -0.7232164 -0.1736486 0.9985       NA
#> 2    BioA - BioC -0.7285184 -1.1377952 -0.3161147 1.0000       NA
#> 3 BioA - Control  0.1922842 -0.1240058  0.4941931 0.8840       NA
#> 4    BioB - BioC -0.2934343 -0.6596813  0.1083947 0.9185       NA
#> 5 BioB - Control  0.6365151  0.3511569  0.8869802 1.0000       NA
#> 6 BioC - Control  0.9263239  0.4756512  1.3028233 1.0000       NA
#>   p_abs_gt_meaningful
#> 1                  NA
#> 2                  NA
#> 3                  NA
#> 4                  NA
#> 5                  NA
#> 6                  NA
```

Each contrast is computed draw by draw, preserving joint posterior
uncertainty across group functionals.

## 9. Probability of direction

The `pd` column reports the larger posterior probability that a contrast
is positive or negative. It is an index of directional certainty, not an
effect-size measure. The dedicated ROPE vignette explains why direction,
magnitude, practical relevance, and Bayes-factor evidence should not be
collapsed into one label (Makowski et al. 2019).

## 10. ROPE for practical equivalence

``` r

con_rope <- bayes_bootstrap_contrasts(bb, rope = c(-0.20, 0.20))
con_rope
#>         contrast     median      lower      upper     pd rope_pct
#> 1    BioA - BioB -0.4421089 -0.7232164 -0.1736486 0.9985   0.0405
#> 2    BioA - BioC -0.7285184 -1.1377952 -0.3161147 1.0000   0.0065
#> 3 BioA - Control  0.1922842 -0.1240058  0.4941931 0.8840   0.5145
#> 4    BioB - BioC -0.2934343 -0.6596813  0.1083947 0.9185   0.3150
#> 5 BioB - Control  0.6365151  0.3511569  0.8869802 1.0000   0.0015
#> 6 BioC - Control  0.9263239  0.4756512  1.3028233 1.0000   0.0015
#>   p_abs_gt_meaningful
#> 1                  NA
#> 2                  NA
#> 3                  NA
#> 4                  NA
#> 5                  NA
#> 6                  NA
```

The interval must be justified in response units. A universal default
ROPE would be scientifically inappropriate because 0.2 units can be
negligible in one measurement system and consequential in another.

## 11. Meaningful-effect thresholds

``` r

con_mid <- bayes_bootstrap_contrasts(bb, meaningful = 0.50)
con_mid
#>         contrast     median      lower      upper     pd rope_pct
#> 1    BioA - BioB -0.4421089 -0.7232164 -0.1736486 0.9985       NA
#> 2    BioA - BioC -0.7285184 -1.1377952 -0.3161147 1.0000       NA
#> 3 BioA - Control  0.1922842 -0.1240058  0.4941931 0.8840       NA
#> 4    BioB - BioC -0.2934343 -0.6596813  0.1083947 0.9185       NA
#> 5 BioB - Control  0.6365151  0.3511569  0.8869802 1.0000       NA
#> 6 BioC - Control  0.9263239  0.4756512  1.3028233 1.0000       NA
#>   p_abs_gt_meaningful
#> 1              0.3280
#> 2              0.8470
#> 3              0.0235
#> 4              0.1355
#> 5              0.8395
#> 6              0.9700
```

`p_abs_gt_meaningful` asks how much posterior mass lies beyond an
absolute meaningful-effect threshold. Direction-specific thresholds can
be calculated directly from the stored draw matrix when the scientific
decision is asymmetric.

## 12. Reproducibility

``` r

a <- bayes_bootstrap(d, "yield", "treatment", draws = 500, seed = 17)
b <- bayes_bootstrap(d, "yield", "treatment", draws = 500, seed = 17)
identical(a$draws, b$draws)
#> [1] TRUE
```

The explicit seed is stored in the object.

## 13. Why this is not the classical bootstrap

The classical nonparametric bootstrap repeatedly samples observations
with replacement. The Bayesian bootstrap keeps the observed support
fixed and samples posterior probability weights. Although their
large-sample behavior can be related for some functionals, they
represent different probability constructions and should not be
described as identical methods.

## 14. Why this is not a rank test

A rank test has a sampling-distribution target defined under a
frequentist null hypothesis. The Bayesian bootstrap instead produces
posterior uncertainty for empirical-distribution functionals. If the
scientific question concerns stochastic ordering, medians, quantiles, or
probability of superiority, define that estimand directly rather than
calling the procedure a Bayesian Mann-Whitney test.

## 15. Why this is not a generic Bayesian ANOVA

The current grouped bootstrap treats each group distribution separately
and compares posterior functionals. It does not automatically account
for blocks, repeated measurements, split-plot randomization, covariates,
or hierarchical shrinkage. Those designs require the corresponding
model-aware workflow.

## 16. Blocking cannot be ignored

For a randomized complete block design, observations within a block
share design structure. Applying the grouped Bayesian bootstrap only by
treatment discards that pairing. Use
[`bayes_rcbd()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_rcbd.md)
with the hierarchical model when block variation is part of the
experiment.

## 17. Repeated measures cannot be ignored

Similarly, repeated observations on a plot are dependent. A naive
empirical-weight bootstrap over rows treats measurements as exchangeable
and is not the package’s recommended repeated-measures method. Use
[`bayes_repeated()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_repeated.md)
to model the subject and temporal structure.

## 18. Small-sample interpretation

With very few unique observations, the posterior remains supported only
on the observed values. This can be a useful feature for transparent
sensitivity analysis, but it also means the method cannot learn tail
behavior or values not represented in the empirical support. Report the
sample size and observed support clearly.

## 19. Outliers

An extreme observation can receive substantial posterior weight in some
draws. That is not a software defect. The correct response is to verify
data quality, understand the measurement process, and consider a
scientifically justified robust likelihood or median estimand rather
than deleting the point because it is inconvenient.

## 20. Missing data

The current implementation requires complete responses. Missing outcomes
should be handled by an explicit missing-data model or principled
preprocessing plan. The package does not silently remove rows because
doing so can change both the estimand and uncertainty.

## 21. Sensitivity to the estimand

Compare posterior means and medians only when both answer scientifically
relevant questions. A change in conclusion indicates that distribution
shape matters and should motivate closer inspection, not automatic
selection of whichever statistic supports the desired story.

## 22. Visualizing posterior group functionals

The draw matrix can be converted to long form for density or interval
plots. Always retain observed group data in the visualization when
possible so readers can distinguish observed variation from posterior
uncertainty in a summary functional.

## 23. Pairwise multiplicity

Bayesian posterior probabilities do not require a mechanical frequentist
multiplicity correction, but a large family of exploratory contrasts
still creates interpretive and decision problems. Pre-specify
scientifically meaningful contrasts when the analysis is confirmatory
and report the full comparison family when exploration is broad.

## 24. Decision thresholds

A rule such as `P(Delta > MID | data) > 0.95` is a decision rule, not a
universal law of Bayesian inference. The threshold and loss implications
should be justified before treating it as a recommendation.

## 25. ROPE sensitivity

Evaluate several scientifically defensible ROPE widths when the
practical-equivalence boundary is uncertain. Report whether the
substantive interpretation changes. This parallels broader Bayesian
sensitivity analysis recommended in reporting guidance (Kruschke 2021).

## 26. Simulation validation

Because the method is simple, it is valuable to simulate known group
distributions and examine bias, posterior interval behavior, and
treatment-ranking recovery over realistic sample sizes. Simulations
should preserve the intended design rather than treating all rows as
independent if the future target is hierarchical.

## 27. A complete grouped workflow

``` r

d <- bayes_teaching_data("crd")
bb <- bayes_bootstrap(d, "yield", "treatment", draws = 4000, seed = 20260824)
summary_table <- bb$summary
contrast_table <- bayes_bootstrap_contrasts(
  bb, rope = c(-0.2, 0.2), meaningful = 0.5
)
summary_table
#>     group     mean   median         sd    lower    upper
#> 1    BioA 6.245873 6.240116 0.11586870 6.034761 6.494830
#> 2    BioB 6.680349 6.676747 0.08554293 6.526432 6.860111
#> 3    BioC 6.963002 6.964167 0.18070420 6.611002 7.316458
#> 4 Control 6.049769 6.038552 0.10914309 5.865351 6.296810
contrast_table
#>         contrast     median      lower      upper      pd rope_pct
#> 1    BioA - BioB -0.4382179 -0.7149942 -0.1461919 0.99825  0.05575
#> 2    BioA - BioC -0.7198307 -1.1369322 -0.2910640 0.99925  0.00925
#> 3 BioA - Control  0.1987361 -0.1220030  0.5005273 0.89525  0.49575
#> 4    BioB - BioC -0.2872268 -0.6681371  0.1148174 0.92225  0.32825
#> 5 BioB - Control  0.6362984  0.3405225  0.8906700 1.00000  0.00225
#> 6 BioC - Control  0.9180726  0.4779031  1.3192258 1.00000  0.00100
#>   p_abs_gt_meaningful
#> 1             0.32250
#> 2             0.84675
#> 3             0.02525
#> 4             0.13825
#> 5             0.82950
#> 6             0.96950
```

## 28. When to prefer a parametric or semiparametric model

Prefer a model-aware likelihood when the experimental design contains
blocks or repeated units, when covariates are essential, when prediction
outside observed values is required, when variance components are
scientific targets, or when a distributional model represents the
measurement process more clearly.

## 29. When the Bayesian bootstrap is useful

It is especially useful as a teaching device for posterior uncertainty
without a conventional parametric likelihood, as a sensitivity analysis
for simple independent groups, and for posterior uncertainty of
functionals such as means or medians when the empirical support is an
acceptable representation of the target distribution.

## 30. Reporting

Report the functional, grouping structure, number of posterior draws,
random seed, credible mass, all practical thresholds, and why
exchangeability of observations is defensible. State explicitly that the
analysis uses Rubin’s Bayesian bootstrap and not a generic Bayesian
ANOVA.

## 31. Scope of version 1.0.0

The current implementation provides mean and median functionals and
pairwise grouped contrasts. Future extensions can add quantiles, ratios,
probabilities of superiority, stratified/pair-aware weights, and
user-supplied functionals, but those additions must preserve design
awareness rather than silently generalizing an independent-observation
method.

## References

The method follows Rubin’s original Bayesian bootstrap construction
(Rubin 1981). Interpretation of posterior effect indices follows the
broader distinction among existence, magnitude, and practical relevance
discussed by Makowski et al. (Makowski et al. 2019) and the reporting
principles in BARG (Kruschke 2021). \# Reproducibility checklist

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

## Worked sensitivity exercise: mean versus median

A useful exercise is to examine how the scientific conclusion changes
when the estimand changes for a defensible reason. The point is not to
choose the functional that produces a more attractive comparison. It is
to understand which aspect of the response distribution is
scientifically important.

``` r

bb_mean <- bayes_bootstrap(
  d, "yield", "treatment",
  statistic = "mean", draws = 1000, seed = 41
)

bb_median <- bayes_bootstrap(
  d, "yield", "treatment",
  statistic = "median", draws = 1000, seed = 41
)

bb_mean$summary
#>     group     mean   median         sd    lower    upper
#> 1    BioA 6.247624 6.239368 0.11838790 6.021917 6.490772
#> 2    BioB 6.680299 6.675687 0.08176426 6.531869 6.849073
#> 3    BioC 6.971921 6.977345 0.17991160 6.626673 7.307331
#> 4 Control 6.049944 6.037885 0.10934338 5.863024 6.300646
bb_median$summary
#>     group     mean   median        sd    lower    upper
#> 1    BioA 6.209972 6.124542 0.1825752 5.858721 6.600682
#> 2    BioB 6.635765 6.733959 0.1639463 6.447090 6.982219
#> 3    BioC 7.013425 7.120278 0.3179926 6.494981 7.647230
#> 4 Control 5.966080 5.931464 0.1653961 5.799554 6.388969
```

For each treatment, compare the posterior center and interval. A large
mean-median discrepancy is a prompt to inspect the observed
distribution, measurement process, and possible skewness. It is not, by
itself, evidence that either estimand is invalid.

## Worked sensitivity exercise: practical thresholds

Suppose domain knowledge suggests that differences smaller than 0.20
response units are practically negligible, whereas an absolute
difference of at least 0.50 units is scientifically consequential. The
two thresholds answer different questions and should both remain
visible.

``` r

bb_thresholds <- bayes_bootstrap_contrasts(
  bb_mean,
  rope = c(-0.20, 0.20),
  meaningful = 0.50
)

bb_thresholds
#>         contrast     median      lower      upper    pd rope_pct
#> 1    BioA - BioB -0.4349743 -0.7170286 -0.1414484 1.000    0.054
#> 2    BioA - BioC -0.7267893 -1.1027072 -0.3074582 1.000    0.005
#> 3 BioA - Control  0.1997626 -0.1095275  0.5080423 0.890    0.496
#> 4    BioB - BioC -0.2960940 -0.6511485  0.1092315 0.926    0.329
#> 5 BioB - Control  0.6394122  0.3405720  0.8849153 1.000    0.005
#> 6 BioC - Control  0.9243038  0.4676034  1.3383045 1.000    0.000
#>   p_abs_gt_meaningful
#> 1               0.317
#> 2               0.853
#> 3               0.029
#> 4               0.152
#> 5               0.821
#> 6               0.968
```

Interpret the columns separately:

1.  `median` is a posterior contrast location;
2.  `lower` and `upper` quantify posterior uncertainty;
3.  `pd` expresses directional certainty;
4.  `rope_pct` expresses posterior compatibility with practical
    equivalence;
5.  `p_abs_gt_meaningful` expresses posterior probability of exceeding
    the meaningful absolute threshold.

No single column is a replacement for the others.

## Worked sensitivity exercise: Monte Carlo resolution

The Bayesian bootstrap itself is inexpensive, so there is little reason
to use very few posterior weight draws in a final analysis. A simple
Monte Carlo stability exercise can compare summaries at increasing draw
counts.

``` r

bb_500 <- bayes_bootstrap(d, "yield", "treatment", draws = 500, seed = 51)
bb_2000 <- bayes_bootstrap(d, "yield", "treatment", draws = 2000, seed = 51)

bb_500$summary
#>     group     mean   median         sd    lower    upper
#> 1    BioA 6.240757 6.240905 0.11287408 6.025159 6.458431
#> 2    BioB 6.683209 6.679232 0.08721884 6.532073 6.864637
#> 3    BioC 6.973427 6.983400 0.18550519 6.579212 7.314882
#> 4 Control 6.052639 6.041644 0.10675993 5.880201 6.281811
bb_2000$summary
#>     group     mean   median         sd    lower    upper
#> 1    BioA 6.240814 6.235269 0.11670325 6.023430 6.489033
#> 2    BioB 6.682004 6.678369 0.08648603 6.526195 6.861665
#> 3    BioC 6.973349 6.974800 0.18394334 6.607013 7.323936
#> 4 Control 6.044716 6.032201 0.10891199 5.866931 6.280823
```

Posterior summaries should stabilize as simulation error decreases. If
the scientific conclusion changes materially because 500 versus 2,000
weight draws were used, the final run needs greater Monte Carlo
resolution and the instability should be investigated.

## Suggested simulation study for developers

A package-level simulation can generate groups from symmetric, skewed,
heavy-tailed, and mixture distributions. For each scenario and sample
size, record posterior mean and median bias, interval coverage for the
chosen functional, directional-probability calibration, and sensitivity
to a single extreme observation.

The simulation should also include a negative control in which
observations are generated with block or subject dependence. The purpose
of that control is to show that the independent grouped Bayesian
bootstrap is not a substitute for the design-aware hierarchical model
when exchangeability is violated.

## Final interpretation template

A concise scientific interpretation can follow this structure:

> The Bayesian bootstrap was used to estimate treatment-specific
> \[mean/median\] responses without imposing a parametric response
> distribution. Posterior pairwise differences were summarized by
> credible intervals, directional probability, the posterior mass within
> the prespecified practical-equivalence interval, and the probability
> of exceeding the prespecified meaningful difference. The analysis
> treats observations as exchangeable within groups; therefore its
> conclusions apply only to designs for which that assumption is
> defensible.

The bracketed choices and thresholds must be replaced by the actual
estimand and scientifically justified values used in the analysis.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Makowski, Dominique, Mattan S. Ben-Shachar, S. H. Annabel Chen, and
Daniel Lüdecke. 2019. “Indices of Effect Existence and Significance in
the Bayesian Framework.” *Frontiers in Psychology* 10: 2767.
<https://doi.org/10.3389/fpsyg.2019.02767>.

Rubin, Donald B. 1981. “The Bayesian Bootstrap.” *The Annals of
Statistics* 9 (1): 130–34. <https://doi.org/10.1214/aos/1176345338>.
