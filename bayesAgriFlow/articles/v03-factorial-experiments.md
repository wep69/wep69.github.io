# Bayesian Qualitative Factorial Experiments

## 1. Purpose

Factorial experiments are not collections of unrelated one-factor tests.
Their main value is that they allow the response to one factor to be
studied across the levels of another factor.

`bayesAgriFlow` version 1.0.0 supports qualitative factorial Gaussian
models with all interactions implied by the supplied factor list.
Blocking can also be declared.

The central interpretive rule is:

**Inspect the interaction before reducing the analysis to marginal main
effects.**

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  create and audit a qualitative factorial design;
2.  recognize empty or unbalanced factorial cells;
3.  understand hierarchical model terms such as `A * B`;
4.  distinguish coefficient coding from scientific contrasts;
5.  obtain posterior marginal treatment comparisons;
6.  interpret posterior interaction coefficients;
7.  apply ROPE or meaningful thresholds to comparisons;
8.  request optional model-space Bayes-factor evidence;
9.  understand the limitations of automatic inclusion evidence;
10. prepare the analysis for future simple-effect and higher-order
    contrast extensions.

## 3. Teaching example

The bundled example has two cultivars, three fertilizer treatments, and
four blocks.

``` r

d <- bayes_teaching_data("factorial")
with(d, table(block, cultivar, fertilizer))
#> , , fertilizer = F0
#> 
#>      cultivar
#> block A B
#>    B1 1 1
#>    B2 1 1
#>    B3 1 1
#>    B4 1 1
#> 
#> , , fertilizer = F1
#> 
#>      cultivar
#> block A B
#>    B1 1 1
#>    B2 1 1
#>    B3 1 1
#>    B4 1 1
#> 
#> , , fertilizer = F2
#> 
#>      cultivar
#> block A B
#>    B1 1 1
#>    B2 1 1
#>    B3 1 1
#>    B4 1 1
```

Create the design object.

``` r

des <- bayes_factorial(
  data = d,
  response = "yield",
  factors = c("cultivar", "fertilizer"),
  block = "block"
)

des
#> <bayes_design>
#>   Type: Qualitative factorial design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 24
#>   block: block
#>   factors: cultivar, fertilizer
audit <- bayes_design_audit(des)
audit
#> <bayes_design_audit>
#>   Design: Qualitative factorial design
#>   Status: PASS
```

The audit verifies that all combinations exist and whether the cell
counts are balanced.

## 4. Model structure

For two factors $`A`$ and $`B`$, the Gaussian model can be written

``` math
y_{ijk}
=
\mu
+\alpha_i
+\beta_j
+(\alpha\beta)_{ij}
+b_k
+\varepsilon_{ijk}.
```

The term $`(\alpha\beta)_{ij}`$ means that the effect of one factor is
allowed to depend on the level of the other.

The R formula compiler uses

``` text
cultivar * fertilizer + block
```

for the native path and

``` text
cultivar * fertilizer + (1 | block)
```

for the multilevel `brms` path.

## 5. Fit the posterior model

``` r

fit <- bayes_fit(
  des,
  engine = "native",
  prior = bayes_prior(),
  draws = 2500,
  seed = 20260824
)

fit
#> <bayes_fit>
#>   Design: Qualitative factorial design
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ cultivar * fertilizer + block
#>   Posterior draws: 2500
bayes_summary(fit)
#>                 parameter       mean     median         sd       lower
#> 1             (Intercept) 6.78641260 6.79176752 0.17617956  6.44062635
#> 2               cultivarB 0.44497504 0.44229342 0.20122059  0.05263762
#> 3            fertilizerF1 0.50550977 0.50714959 0.20340147  0.10340210
#> 4            fertilizerF2 0.69541885 0.69530630 0.20358764  0.29017625
#> 5                 blockB2 0.04225248 0.04168358 0.17601795 -0.30790738
#> 6                 blockB3 0.09576371 0.09682413 0.16789965 -0.22517607
#> 7                 blockB4 0.16580818 0.16260066 0.17230675 -0.17264176
#> 8  cultivarB:fertilizerF1 0.13197156 0.13200724 0.28053178 -0.42973905
#> 9  cultivarB:fertilizerF2 0.64781066 0.64922224 0.28177556  0.07879799
#> 10                  sigma 0.31180444 0.30806015 0.04391145  0.23860160
#>        upper p_positive p_negative     pd
#> 1  7.1267258     1.0000     0.0000 1.0000
#> 2  0.8635407     0.9860     0.0140 0.9860
#> 3  0.8894852     0.9904     0.0096 0.9904
#> 4  1.1007626     0.9996     0.0004 0.9996
#> 5  0.4066177     0.5996     0.4004 0.5996
#> 6  0.4432540     0.7172     0.2828 0.7172
#> 7  0.5217060     0.8400     0.1600 0.8400
#> 8  0.6998762     0.6808     0.3192 0.6808
#> 9  1.2013024     0.9880     0.0120 0.9880
#> 10 0.4152169     1.0000     0.0000 1.0000
```

Coefficient names reflect treatment contrasts used by R’s model matrix.
They are useful for reconstructing the linear predictor, but they are
not always the most intuitive quantities for scientific reporting.

## 6. Interpret the interaction first

Suppose the coefficient table includes one or more terms whose names
contain both factor names. Those terms represent departures from an
additive model under the chosen reference coding.

``` r

sm <- bayes_summary(fit)
sm[grepl(":", sm$parameter), ]
#>                parameter      mean    median        sd       lower     upper
#> 8 cultivarB:fertilizerF1 0.1319716 0.1320072 0.2805318 -0.42973905 0.6998762
#> 9 cultivarB:fertilizerF2 0.6478107 0.6492222 0.2817756  0.07879799 1.2013024
#>   p_positive p_negative     pd
#> 8     0.6808     0.3192 0.6808
#> 9     0.9880     0.0120 0.9880
```

A posterior interaction should be interpreted with its magnitude,
interval, and direction probability. It should not be reduced to a
binary label.

A practical workflow is:

1.  inspect interaction terms;
2.  visualize or summarize cell means;
3.  construct scientifically planned comparisons;
4.  only then summarize marginal effects if they remain meaningful.

## 7. Marginal cultivar comparison

Version 1.0.0 standardizes one factor at a time over the observed
distribution of the other variables.

``` r

cultivar_con <- bayes_contrasts(
  fit,
  factor = "cultivar",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

cultivar_con
#>   contrast level_a level_b       mean     median        sd      lower     upper
#> 1    A - B       A       B -0.7049024 -0.7062459 0.1278387 -0.9619761 -0.457242
#>   p_a_gt_b p_a_lt_b pd rope_pct p_abs_gt_meaningful p_gt_meaningful
#> 1        0        1  1   0.0012              0.9908               0
#>   p_lt_minus_meaningful
#> 1                0.9908
```

This is a marginal comparison. It is not a replacement for cultivar
comparisons within each fertilizer level when interaction is important.

## 8. Fertilizer comparisons

``` r

fert_con <- bayes_contrasts(
  fit,
  factor = "fertilizer",
  meaningful = 0.40
)

fert_con
#>   contrast level_a level_b       mean     median        sd      lower
#> 1  F0 - F1      F0      F1 -0.5714956 -0.5699699 0.1504070 -0.8573889
#> 2  F0 - F2      F0      F2 -1.0193242 -1.0219386 0.1514343 -1.3024623
#> 3  F1 - F2      F1      F2 -0.4478286 -0.4472356 0.1538903 -0.7566846
#>        upper p_a_gt_b p_a_lt_b     pd rope_pct p_abs_gt_meaningful
#> 1 -0.2837384   0.0004   0.9996 0.9996       NA              0.8776
#> 2 -0.7127282   0.0000   1.0000 1.0000       NA              0.9996
#> 3 -0.1519028   0.0028   0.9972 0.9972       NA              0.6208
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1               0                0.8776
#> 2               0                0.9996
#> 3               0                0.6208
```

A contrast labelled `F1 - F0` is summarized by posterior draws of the
population-level difference after standardizing over the observed
cultivar and block distribution represented by the fixed-design model.

## 9. Direction versus practical importance

A high `pd` value means that most posterior mass is on one side of zero.
It does not show that the effect exceeds a useful agronomic difference.

For a threshold $`\delta`$,

``` math
P(|\Delta|>\delta\mid y)
```

asks a direct magnitude question.

``` r

bayes_practical_significance(
  fit,
  threshold = 0.40,
  factor = "fertilizer"
)
#>   contrast     median      lower      upper     pd p_abs_gt_meaningful
#> 1  F0 - F1 -0.5699699 -0.8573889 -0.2837384 0.9996              0.8776
#> 2  F0 - F2 -1.0219386 -1.3024623 -0.7127282 1.0000              0.9996
#> 3  F1 - F2 -0.4472356 -0.7566846 -0.1519028 0.9972              0.6208
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1               0                0.8776
#> 2               0                0.9996
#> 3               0                0.6208
```

The threshold should be chosen before interpreting the posterior
whenever possible.

## 10. Equivalence

When two fertilizer levels would be operationally interchangeable if
their yield difference were between $`-0.20`$ and $`0.20`$, the question
becomes

``` math
P(-0.20\leq\Delta\leq0.20\mid y).
```

``` r

bayes_equivalence(
  fit,
  range = c(-0.20, 0.20),
  probability = 0.95,
  factor = "fertilizer"
)
#>   contrast     median      lower      upper     pd rope_pct
#> 1  F0 - F1 -0.5699699 -0.8573889 -0.2837384 0.9996   0.0088
#> 2  F0 - F2 -1.0219386 -1.3024623 -0.7127282 1.0000   0.0000
#> 3  F1 - F2 -0.4472356 -0.7566846 -0.1519028 0.9972   0.0492
#>   required_probability equivalent_by_rule                  decision_rule
#> 1                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 2                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 3                 0.95              FALSE Posterior mass in ROPE >= 0.95
```

A result that does not satisfy the chosen 0.95 rule is not automatically
evidence of a meaningful difference. It means that this specific
equivalence decision rule was not satisfied.

## 11. Prior predictive and posterior predictive checking

``` r

prior_check <- bayes_prior_predictive(
  des,
  bayes_prior(),
  draws = 200
)
prior_check$summary
#>   statistic      value
#> 1   minimum -17.011528
#> 2    median   7.807709
#> 3   maximum  23.579623
#> 4   mean_sd   1.842700

ppc <- bayes_pp_check(fit, ndraws = 200)
ppc$observed
#>         mean        sd  minimum  maximum      q10      q90
#> 10% 7.613559 0.6496065 6.496105 8.971415 7.007326 8.515822
summary(ppc$replicated$mean)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   7.394   7.543   7.610   7.611   7.678   7.820
```

For a factorial experiment, useful custom checks include:

- replicated cell means;
- replicated interaction contrasts;
- cell-specific variances;
- extreme responses in each treatment combination.

The stored replicated-draw matrix can be used to construct these checks.

## 12. Optional inclusion Bayes factors

``` r

if (requireNamespace("BayesFactor", quietly = TRUE)) {
  fit_bf <- bayes_fit(des, engine = "bayesfactor")
  bayes_evidence(fit_bf)
  bayes_inclusion_bf(fit_bf)
}
```

Inclusion evidence is conditional on the enumerated model set. A model
space containing implausible combinations should not be treated as if it
were a scientifically pre-specified theory space.

For confirmatory work, the preferable route is to define a small number
of scientifically meaningful models or hypotheses.

## 13. Stan path

``` r

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 4,
  chains = 4,
  parallel_chains = 4
)

fit_stan <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior(),
  compute = cmp
)

bayes_diagnose(fit_stan)
bayes_contrasts(fit_stan, "fertilizer")
```

The `brms` path also supports current extensions involving non-Gaussian
responses, heterogeneous variance, random slopes, or more complex
hierarchies.

## 14. Why version 1.0.0 does not automatically print compact letters

Posterior pairwise probabilities can be converted into grouping letters
only after a decision rule is defined. Different rules can produce
different groups:

- posterior probability of superiority above a threshold;
- posterior probability outside a ROPE;
- probability of exceeding a meaningful difference;
- a combination of these criteria.

Therefore, version 1.0.0 leaves the posterior contrast table explicit
rather than hiding it behind letters. A future grouping helper can be
added once the decision rule is made fully auditable.

## 15. Future simple-effect syntax

The object architecture is prepared for a future interface such as:

``` r

# planned, not implemented in 1.0.0
# bayes_contrasts(
#   fit,
#   factor = "cultivar",
#   by = "fertilizer"
# )
```

Version 1.0.0 deliberately rejects unsupported semantics instead of
silently producing a marginal comparison when the user intended a
conditional one.

## 16. Reporting checklist

A factorial Bayesian analysis should report:

- factors and levels;
- experimental unit and blocks;
- balance or missing cells;
- likelihood and model formula;
- priors and prior predictive assessment;
- interaction estimates;
- planned contrasts;
- credible intervals;
- direction probabilities only when useful;
- meaningful thresholds or ROPE with justification;
- posterior predictive assessment;
- prior sensitivity;
- sampler diagnostics for MCMC models;
- explicit models and priors when Bayes factors are reported.

## References
