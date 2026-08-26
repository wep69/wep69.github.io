# ROPE, Effect Indices, Equivalence, and Bayesian Treatment Comparisons

## 1. Why a dedicated vignette is necessary

A Bayesian treatment comparison can generate many numerical summaries.
The danger is to place them in one table and act as if they all measure
the same thing.

They do not.

Consider a posterior distribution for a treatment contrast

``` math
\Delta=\mu_A-\mu_B.
```

At least six different scientific questions can be asked.

1.  **Location:** Where is the posterior centered?
2.  **Uncertainty:** Which values remain plausible?
3.  **Direction:** Is $`A`$ probably larger than $`B`$?
4.  **Practical equivalence:** Is the difference small enough to be
    negligible?
5.  **Meaningful magnitude:** Is the difference large enough to matter?
6.  **Relative model evidence:** Do the data favor one explicitly
    specified hypothesis/model over another?

`bayesAgriFlow` keeps these questions separate. This vignette explains
the indices used by version 1.0.0 and how they should be combined
without turning Bayesian analysis into another threshold-only workflow.

The distinction between indices of effect direction, practical
relevance, and Bayes-factor evidence is also emphasized in the Bayesian
effect-index literature (Makowski et al. 2019). BARG recommends that
decisions about null values be accompanied by actual parameter estimates
and uncertainty (Kruschke 2021).

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  identify the estimand before choosing an index;
2.  distinguish posterior mean, median, and interval summaries;
3.  interpret $`P(\Delta>0\mid y)`$ and probability of direction;
4.  understand why direction probability is not effect magnitude;
5.  define a ROPE in scientific units;
6.  distinguish posterior mass in ROPE from a credible-interval decision
    rule;
7.  use
    [`bayes_equivalence()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_equivalence.md)
    with an explicit probability threshold;
8.  define a minimum meaningful effect $`\delta`$;
9.  calculate $`P(|\Delta|>\delta\mid y)`$;
10. distinguish equivalence, inferiority, superiority, and inconclusive
    posterior patterns;
11. interpret a scale-standardized coefficient cautiously;
12. distinguish BF from posterior probability and effect size;
13. understand the role of prior odds;
14. use prior sensitivity when an inferential index may depend on prior
    choice;
15. create a treatment-comparison table that preserves the meaning of
    each column;
16. avoid universal ROPE or BF cutoffs.

## 3. Start with an estimand, not an index

Load the CRD example.

``` r

d <- bayes_teaching_data("crd")
des <- bayes_crd(d, "yield", "treatment")
fit <- bayes_fit(des, draws = 3000, seed = 20260824)
```

The primary pairwise estimand is a difference between population-level
treatment means.

``` r

con <- bayes_contrasts(fit, factor = "treatment", keep_draws = TRUE)
con
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4089600 -0.4097863 0.1929963 -0.7817730
#> 2    BioA - BioC    BioA    BioC -0.6929933 -0.6882583 0.1972293 -1.0823465
#> 3 BioA - Control    BioA Control  0.2118407  0.2115174 0.1912908 -0.1644165
#> 4    BioB - BioC    BioB    BioC -0.2840333 -0.2794681 0.2035998 -0.6916716
#> 5 BioB - Control    BioB Control  0.6208007  0.6225726 0.1958619  0.2182861
#> 6 BioC - Control    BioC Control  0.9048340  0.9041640 0.2008754  0.5065421
#>         upper     p_a_gt_b     p_a_lt_b        pd rope_pct p_abs_gt_meaningful
#> 1 -0.02386451 0.0196666667 0.9803333333 0.9803333       NA                  NA
#> 2 -0.29014782 0.0003333333 0.9996666667 0.9996667       NA                  NA
#> 3  0.58602984 0.8660000000 0.1340000000 0.8660000       NA                  NA
#> 4  0.11499089 0.0783333333 0.9216666667 0.9216667       NA                  NA
#> 5  1.00049531 0.9996666667 0.0003333333 0.9996667       NA                  NA
#> 6  1.28814778 1.0000000000 0.0000000000 1.0000000       NA                  NA
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1              NA                    NA
#> 2              NA                    NA
#> 3              NA                    NA
#> 4              NA                    NA
#> 5              NA                    NA
#> 6              NA                    NA
```

Every index in the row must refer to the same $`\Delta`$. If one column
refers to a link-scale coefficient and another to a response-scale
treatment difference, interpretation becomes incoherent.

## 4. Posterior location

For each contrast, version 1.0.0 reports both posterior mean and median.

The posterior mean is

``` math
E(\Delta\mid y),
```

whereas the posterior median $`m`$ satisfies

``` math
P(\Delta\le m\mid y)\ge 0.5
```

and

``` math
P(\Delta\ge m\mid y)\ge 0.5.
```

For approximately symmetric posteriors they are similar. For skewed
posteriors they can differ.

The median is often convenient for a compact report because it is
directly linked to posterior quantiles. The mean is useful for
expected-loss calculations under squared-error loss.

Neither one should be reported without uncertainty.

## 5. Credible intervals

Version 1.0.0 uses equal-tailed intervals computed from posterior
quantiles.

For a 95% interval,

``` math
P\left(
q_{0.025}\le\Delta\le q_{0.975}
\mid y
\right)
=0.95.
```

This has a direct posterior-probability interpretation conditional on
the model and priors.

``` r

con[c("contrast", "median", "lower", "upper")]
#>         contrast     median      lower       upper
#> 1    BioA - BioB -0.4097863 -0.7817730 -0.02386451
#> 2    BioA - BioC -0.6882583 -1.0823465 -0.29014782
#> 3 BioA - Control  0.2115174 -0.1644165  0.58602984
#> 4    BioB - BioC -0.2794681 -0.6916716  0.11499089
#> 5 BioB - Control  0.6225726  0.2182861  1.00049531
#> 6 BioC - Control  0.9041640  0.5065421  1.28814778
```

The interval is not a frequentist confidence interval. The frequentist
crosswalk may display a confidence interval elsewhere, but the two
intervals have different probability interpretations (Doorn et al.
2021).

Highest-density intervals are also common in Bayesian work. Version
1.0.0 does not relabel its equal-tailed interval as an HDI. Users who
need HDIs can access posterior draws and use an appropriate
posterior-summary package.

## 6. Direction probability

For $`A-B`$,

``` math
P(\Delta>0\mid y)
```

is reported as `p_a_gt_b`, and

``` math
P(\Delta<0\mid y)
```

as `p_a_lt_b`.

Probability of direction (`pd`) is

``` math
pd=
\max\{
P(\Delta>0\mid y),
P(\Delta<0\mid y)
\}.
```

``` r

con[c("contrast", "p_a_gt_b", "p_a_lt_b", "pd")]
#>         contrast     p_a_gt_b     p_a_lt_b        pd
#> 1    BioA - BioB 0.0196666667 0.9803333333 0.9803333
#> 2    BioA - BioC 0.0003333333 0.9996666667 0.9996667
#> 3 BioA - Control 0.8660000000 0.1340000000 0.8660000
#> 4    BioB - BioC 0.0783333333 0.9216666667 0.9216667
#> 5 BioB - Control 0.9996666667 0.0003333333 0.9996667
#> 6 BioC - Control 1.0000000000 0.0000000000 1.0000000
```

This is useful for the narrow question:

> Which sign is more probable?

It does **not** answer:

> Is the effect large?

A posterior concentrated around $`0.01`$ can have very high `pd` while
the effect is negligible for agricultural practice.

## 7. Superiority probability

For a contrast `A - B`, `p_a_gt_b` is also a posterior probability of
superiority under the simple ordering criterion $`A>B`$.

This is sometimes called a common-language probability because it can be
communicated as:

> Given the model, priors, and data, the posterior probability that the
> standardized mean response under A exceeds that under B is …

Use the treatment-mean estimand actually calculated by the package. Do
not reinterpret this number as the probability that a randomly selected
individual plant under A will exceed a randomly selected plant under B.
That is a different predictive estimand.

## 8. Why zero is not always the scientifically important boundary

Many agricultural decisions are not changed by an arbitrarily small
difference.

Suppose two fertilizer programs differ by 0.03 Mg/ha. If that difference
is smaller than measurement variation, operational resolution, and
economic relevance, the scientific question is not merely whether the
posterior lies above zero.

This motivates an equivalence region.

## 9. Define ROPE in the units of the estimand

Let

``` math
\mathrm{ROPE}=[-\delta_0,\delta_0]
```

represent treatment differences considered practically negligible.

For the simulated example, consider only for illustration

``` math
[-0.20,\ 0.20].
```

``` r

rope_res <- bayes_rope(
  fit,
  range = c(-0.20, 0.20),
  factor = "treatment"
)

rope_res
#>         contrast     median      lower       upper        pd     rope_pct
#> 1    BioA - BioB -0.4097863 -0.7817730 -0.02386451 0.9803333 0.1303333333
#> 2    BioA - BioC -0.6882583 -1.0823465 -0.29014782 0.9996667 0.0060000000
#> 3 BioA - Control  0.2115174 -0.1644165  0.58602984 0.8660000 0.4590000000
#> 4    BioB - BioC -0.2794681 -0.6916716  0.11499089 0.9216667 0.3173333333
#> 5 BioB - Control  0.6225726  0.2182861  1.00049531 0.9996667 0.0203333333
#> 6 BioC - Control  0.9041640  0.5065421  1.28814778 1.0000000 0.0006666667
```

`rope_pct` is

``` math
P(-0.20\le\Delta\le0.20\mid y).
```

The limits are not defaults recommended for agronomy. They are teaching
values on this simulated scale.

For a real study, possible sources for a ROPE include:

- agronomic minimum differences that change management;
- analytical or measurement precision;
- crop-value and input-cost relationships;
- regulatory tolerance;
- biologically negligible changes established from prior studies;
- a pre-specified smallest effect size of interest.

The rationale should be reported.

## 10. Full posterior mass versus interval-only ROPE rules

There are several ways to combine credible intervals and ROPE.

One rule asks whether the entire credible interval lies inside the ROPE.
Another directly calculates posterior mass inside the ROPE.

`bayesAgriFlow` version 1.0.0 reports full posterior mass inside the
ROPE. This preserves the probability quantity rather than converting the
analysis immediately to a binary interval-overlap label.

For example:

``` r

eq <- bayes_equivalence(
  fit,
  range = c(-0.20, 0.20),
  probability = 0.95,
  factor = "treatment"
)

eq
#>         contrast     median      lower       upper        pd     rope_pct
#> 1    BioA - BioB -0.4097863 -0.7817730 -0.02386451 0.9803333 0.1303333333
#> 2    BioA - BioC -0.6882583 -1.0823465 -0.29014782 0.9996667 0.0060000000
#> 3 BioA - Control  0.2115174 -0.1644165  0.58602984 0.8660000 0.4590000000
#> 4    BioB - BioC -0.2794681 -0.6916716  0.11499089 0.9216667 0.3173333333
#> 5 BioB - Control  0.6225726  0.2182861  1.00049531 0.9996667 0.0203333333
#> 6 BioC - Control  0.9041640  0.5065421  1.28814778 1.0000000 0.0006666667
#>   required_probability equivalent_by_rule                  decision_rule
#> 1                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 2                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 3                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 4                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 5                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 6                 0.95              FALSE Posterior mass in ROPE >= 0.95
```

The rule is explicit:

``` math
P(\Delta\in\mathrm{ROPE}\mid y)\ge0.95.
```

Changing 0.95 to 0.90 changes the decision rule. The data and posterior
do not change.

## 11. Four useful posterior states

Using a ROPE, a posterior can show conceptually different patterns.

### 11.1 Practical equivalence

Most posterior mass lies inside the ROPE.

Interpretation:

> The model assigns high posterior probability to differences small
> enough to be considered practically negligible under the stated ROPE.

### 11.2 Meaningful positive effect

Most posterior mass lies above the upper ROPE limit or above a separate
meaningful threshold.

### 11.3 Meaningful negative effect

Most posterior mass lies below the lower limit.

### 11.4 Inconclusive effect

Substantial mass lies both inside and outside the ROPE, or spans
positive and negative regions.

“Inconclusive” is not a failure of Bayesian analysis. It is a legitimate
description of uncertainty.

## 12. Meaningful-effect thresholds

Sometimes the scientific question is not equivalence around zero but
whether the magnitude exceeds a positive threshold.

Let $`\delta_m\ge0`$ be the minimum meaningful difference.

Then calculate

``` math
P(|\Delta|>\delta_m\mid y).
```

``` r

meaning <- bayes_practical_significance(
  fit,
  threshold = 0.40,
  factor = "treatment"
)

meaning
#>         contrast     median      lower       upper        pd
#> 1    BioA - BioB -0.4097863 -0.7817730 -0.02386451 0.9803333
#> 2    BioA - BioC -0.6882583 -1.0823465 -0.29014782 0.9996667
#> 3 BioA - Control  0.2115174 -0.1644165  0.58602984 0.8660000
#> 4    BioB - BioC -0.2794681 -0.6916716  0.11499089 0.9216667
#> 5 BioB - Control  0.6225726  0.2182861  1.00049531 0.9996667
#> 6 BioC - Control  0.9041640  0.5065421  1.28814778 1.0000000
#>   p_abs_gt_meaningful p_gt_meaningful p_lt_minus_meaningful
#> 1           0.5196667       0.0000000             0.5196667
#> 2           0.9340000       0.0000000             0.9340000
#> 3           0.1580000       0.1560000             0.0020000
#> 4           0.2616667       0.0010000             0.2606667
#> 5           0.8786667       0.8786667             0.0000000
#> 6           0.9923333       0.9923333             0.0000000
```

The output also retains directional probabilities

``` math
P(\Delta>\delta_m\mid y)
```

and

``` math
P(\Delta<-\delta_m\mid y).
```

This is useful when positive and negative effects have different
interpretations.

## 13. ROPE and meaningful threshold need not be the same number

A scientifically realistic decision framework can contain three zones:

``` math
\Delta < -\delta_m,
```

``` math
-\delta_0\le\Delta\le\delta_0,
```

and

``` math
\Delta > \delta_m,
```

with an intermediate region between $`\delta_0`$ and $`\delta_m`$.

The intermediate region means “not negligible, but not large enough to
meet the chosen meaningful-effect criterion.”

That is often more informative than forcing every effect into “null” or
“important”.

## 14. Sensitivity to the ROPE itself

ROPE limits can be uncertain. A useful analysis therefore evaluates
several scientifically defensible limits.

``` r

r1 <- bayes_rope(fit, c(-0.10, 0.10), factor = "treatment")
r2 <- bayes_rope(fit, c(-0.20, 0.20), factor = "treatment")
r3 <- bayes_rope(fit, c(-0.30, 0.30), factor = "treatment")

merge(
  r1[c("contrast", "rope_pct")],
  r2[c("contrast", "rope_pct")],
  by = "contrast",
  suffixes = c("_rope10", "_rope20")
)
#>         contrast rope_pct_rope10 rope_pct_rope20
#> 1    BioA - BioB    0.0440000000    0.1303333333
#> 2    BioA - BioC    0.0006666667    0.0060000000
#> 3 BioA - Control    0.2266666667    0.4590000000
#> 4    BioB - BioC    0.1390000000    0.3173333333
#> 5 BioB - Control    0.0060000000    0.0203333333
#> 6 BioC - Control    0.0003333333    0.0006666667
```

A full report can add the third result as well.

This is not an invitation to choose the ROPE that produces the preferred
answer. The set of plausible ROPEs should be motivated scientifically.

## 15. Standardized effect summaries

``` r

bayes_effects(fit)
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.2600480  6.2588457 0.13473931  5.99477952 6.5354257
#> 2    treatmentBioB  0.4089600  0.4097863 0.19299627  0.02386451 0.7817730
#> 3    treatmentBioC  0.6929933  0.6882583 0.19722935  0.29014782 1.0823465
#> 4 treatmentControl -0.2118407 -0.2115174 0.19129080 -0.58602984 0.1644165
#> 5            sigma  0.3969569  0.3931142 0.04919018  0.31330950 0.5109398
#>   p_positive   p_negative        pd standardized_median
#> 1  1.0000000 0.0000000000 1.0000000          11.6858806
#> 2  0.9803333 0.0196666667 0.9803333           0.7651114
#> 3  0.9996667 0.0003333333 0.9996667           1.2850460
#> 4  0.1340000 0.8660000000 0.8660000          -0.3949239
#> 5  1.0000000 0.0000000000 1.0000000                  NA
#>                                                                              effect_index_note
#> 1 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 2 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 3 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 4 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 5                                                   Scale, dispersion, or dependence parameter
```

For regression coefficients, version 1.0.0 includes a descriptive
`standardized_median` obtained by dividing the posterior median by the
observed response SD.

This is useful for scale orientation, but it is not automatically
Cohen’s $`d`$, Hedges’ $`g`$, or a universal agronomic effect size. The
denominator and estimand matter.

For treatment contrasts, the response-scale difference should remain the
primary quantity whenever the original units are scientifically
interpretable.

## 16. Parameter-level hypotheses

The package also exposes directional probabilities for individual
posterior parameters.

``` r

parameter_names <- bayes_summary(fit)$parameter
p <- setdiff(parameter_names, c("(Intercept)", "sigma"))[1]

bayes_hypothesis(
  fit,
  parameter = p,
  value = 0,
  direction = "greater"
)
#>       parameter reference direction posterior_probability
#> 1 treatmentBioB         0   greater             0.9803333
```

Remember that treatment-coded coefficients depend on the reference
parameterization. A planned response-scale treatment contrast is often
easier to interpret.

## 17. Bayes factor: a different axis

If the optional evidence backend is available,

``` r

fit_bf <- bayes_fit(des, engine = "bayesfactor")
bayes_evidence(fit_bf)
```

a BF asks about relative predictive evidence between models.

For $`H_1`$ versus $`H_0`$,

``` math
BF_{10}
=
\frac{p(y\mid H_1)}{p(y\mid H_0)}.
```

This does not tell us how large $`\Delta`$ is.

A complete treatment analysis can therefore report:

``` text
posterior median and interval
P(direction)
P(in ROPE)
P(exceed meaningful threshold)
BF for an explicitly defined model comparison, if scientifically needed
```

Each line answers a separate question.

## 18. Prior odds and posterior model probability

The BF updates model odds:

``` math
O_{10}^{post}
=
BF_{10}O_{10}^{prior}.
```

If prior model odds equal one,

``` math
P(H_1\mid y)
=
\frac{BF_{10}}{1+BF_{10}}
```

for a two-model comparison.

But if prior odds are not one, that shortcut is wrong.

``` r

bayes_evidence(fit_bf, prior_odds = 0.5)
```

The package therefore keeps `BF`, `prior_odds`, and `posterior_odds` in
separate columns.

## 19. Inclusion BF is not a treatment contrast

An inclusion BF asks whether model spaces containing an effect receive
more posterior support than model spaces excluding it.

``` r

bayes_inclusion_bf(fit_bf)
```

A treatment contrast such as $`A-B`$ asks about a difference in
treatment means. An inclusion BF for the treatment term asks whether
models containing the treatment effect are favored relative to models
without it.

Those are related but distinct inferential targets.

## 20. A comparison table that does not collapse concepts

``` r

integrated <- bayes_contrasts(
  fit,
  factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

integrated
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4089600 -0.4097863 0.1929963 -0.7817730
#> 2    BioA - BioC    BioA    BioC -0.6929933 -0.6882583 0.1972293 -1.0823465
#> 3 BioA - Control    BioA Control  0.2118407  0.2115174 0.1912908 -0.1644165
#> 4    BioB - BioC    BioB    BioC -0.2840333 -0.2794681 0.2035998 -0.6916716
#> 5 BioB - Control    BioB Control  0.6208007  0.6225726 0.1958619  0.2182861
#> 6 BioC - Control    BioC Control  0.9048340  0.9041640 0.2008754  0.5065421
#>         upper     p_a_gt_b     p_a_lt_b        pd     rope_pct
#> 1 -0.02386451 0.0196666667 0.9803333333 0.9803333 0.1303333333
#> 2 -0.29014782 0.0003333333 0.9996666667 0.9996667 0.0060000000
#> 3  0.58602984 0.8660000000 0.1340000000 0.8660000 0.4590000000
#> 4  0.11499089 0.0783333333 0.9216666667 0.9216667 0.3173333333
#> 5  1.00049531 0.9996666667 0.0003333333 0.9996667 0.0203333333
#> 6  1.28814778 1.0000000000 0.0000000000 1.0000000 0.0006666667
#>   p_abs_gt_meaningful p_gt_meaningful p_lt_minus_meaningful
#> 1           0.5196667       0.0000000             0.5196667
#> 2           0.9340000       0.0000000             0.9340000
#> 3           0.1580000       0.1560000             0.0020000
#> 4           0.2616667       0.0010000             0.2606667
#> 5           0.8786667       0.8786667             0.0000000
#> 6           0.9923333       0.9923333             0.0000000
```

A useful manuscript table can include:

| Quantity | Meaning |
|----|----|
| Median | posterior location of the treatment difference |
| 95% CrI | posterior uncertainty interval |
| $`P(A>B)`$ | direction/superiority probability for the reported estimand |
| pd | certainty about sign |
| ROPE % | posterior mass in practical-equivalence region |
| $`P(|\Delta|>\delta_m)`$ | probability of a meaningful magnitude |
| BF | relative evidence for specified models, reported separately |

Avoid a final column called “significant: yes/no” that erases all of
this information.

## 21. A worked interpretation pattern

Suppose, hypothetically, a contrast had the following posterior pattern:

``` text
median = 0.35
95% CrI = [0.05, 0.66]
P(A>B) = 0.99
ROPE mass [-0.20,0.20] = 0.12
P(|Delta| > 0.40) = 0.38
```

A defensible interpretation would be:

> The posterior strongly favors a positive treatment difference, but
> only a minority of posterior mass exceeds the pre-specified 0.40-unit
> meaningful threshold. The estimated effect is therefore directionally
> clear but its probability of reaching the selected practical-magnitude
> criterion remains limited.

Do not replace this with:

> A is significant.

The hypothetical numbers above are illustrative only and are not results
from the bundled teaching dataset.

## 22. Another hypothetical pattern

``` text
median = 0.02
95% CrI = [-0.11, 0.15]
P(A>B) = 0.61
ROPE mass [-0.20,0.20] = 0.98
```

Under a pre-specified 0.95 equivalence rule, the posterior would support
practical equivalence relative to that ROPE.

Again, this is a decision relative to a scientific definition, not a
universal truth that the treatment effects are exactly equal.

## 23. An inconclusive pattern

``` text
median = 0.18
95% CrI = [-0.22, 0.61]
P(A>B) = 0.78
ROPE mass = 0.44
P(|Delta| > meaningful threshold) = 0.29
```

This posterior is not well described by either “no effect” or “important
effect.” The correct communication is uncertainty.

Bayesian methods make this state explicit rather than requiring a binary
conclusion.

## 24. Sensitivity to priors and decisions

BARG recommends sensitivity analysis, including examining whether
decisions change under alternative plausible priors (Kruschke 2021).

For the native engine:

``` r

prior_sens <- bayes_prior_sensitivity(
  des,
  coefficient_scales = c(1, 2.5, 5),
  draws = 1500
)

head(prior_sens)
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.3178720  6.3182349 0.12233635  6.07251155 6.5568117
#> 2    treatmentBioB  0.3252559  0.3217769 0.17502647 -0.00704529 0.6649982
#> 3    treatmentBioC  0.5811452  0.5823941 0.18014276  0.23765020 0.9239996
#> 4 treatmentControl -0.2406574 -0.2367874 0.17442206 -0.59605738 0.1076916
#> 5            sigma  0.4142811  0.4117011 0.04996922  0.32733491 0.5294765
#> 6      (Intercept)  6.2629505  6.2677553 0.13699599  5.99426835 6.5260713
#>   p_positive  p_negative        pd coefficient_scale
#> 1  1.0000000 0.000000000 1.0000000               1.0
#> 2  0.9706667 0.029333333 0.9706667               1.0
#> 3  0.9986667 0.001333333 0.9986667               1.0
#> 4  0.0780000 0.922000000 0.9220000               1.0
#> 5  1.0000000 0.000000000 1.0000000               1.0
#> 6  1.0000000 0.000000000 1.0000000               2.5
```

For a real analysis, repeat the contrast and ROPE calculation under the
candidate priors, not only the coefficient summary.

For Bayes factors, prior sensitivity deserves particular attention
because the marginal likelihood averages over the prior (Tendeiro et al.
2025).

## 25. Why p-values are not added to this table

The frequentist p-value is calculated under a null model as a tail
probability of data or more extreme data. It is not

``` math
P(H_0\mid y),
```

and it is not a posterior direction probability.

The frequentist comparison function is therefore kept in a separate
workflow.

``` r

bayes_compare_frequentist(fit)$anova
#> Analysis of Variance Table
#> 
#> Response: yield
#>           Df Sum Sq Mean Sq F value    Pr(>F)    
#> treatment  3 4.1604 1.38681  8.2057 0.0004488 ***
#> Residuals 28 4.7321 0.16901                      
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

The goal is conceptual comparison, not column-by-column conversion.

## 26. Suggested reporting language

A good Bayesian treatment-comparison paragraph should identify:

1.  the treatment estimand;
2.  posterior central tendency;
3.  credible interval and interval type;
4.  direction probability if relevant;
5.  ROPE limits and scientific rationale if equivalence is evaluated;
6.  minimum meaningful threshold and rationale if used;
7.  BF models and priors if BF is reported;
8.  prior sensitivity;
9.  model and sampler diagnostics;
10. posterior predictive assessment.

Avoid universal verbal categories such as “strong” or “decisive” unless
the actual BF value is also reported and the category is clearly
identified as a communication convention rather than a physical law.

## 27. Complete ROPE workflow

``` r

d <- bayes_teaching_data("rcbd")

des <- bayes_rcbd(
  d,
  response = "yield",
  treatment = "cultivar",
  block = "block"
)

fit <- bayes_fit(
  des,
  engine = "native",
  prior = bayes_prior("regularizing"),
  draws = 5000
)

# Effect estimation
bayes_effects(fit)

# Treatment differences
con <- bayes_contrasts(
  fit,
  factor = "cultivar",
  rope = c(-0.20, 0.20),
  meaningful = 0.30,
  keep_draws = TRUE
)

# Formal equivalence rule
eq <- bayes_equivalence(
  fit,
  range = c(-0.20, 0.20),
  probability = 0.95,
  factor = "cultivar"
)

# Practical magnitude
practical <- bayes_practical_significance(
  fit,
  threshold = 0.30,
  factor = "cultivar"
)

# Predictive adequacy
bayes_pp_check(fit, ndraws = 500)

# Sensitivity
bayes_prior_sensitivity(
  des,
  coefficient_scales = c(1, 2.5, 5),
  draws = 2000
)
```

## 28. Final conceptual map

``` math
\boxed{
\begin{array}{c}
\text{posterior location}\\
\downarrow\\
\text{credible uncertainty}\\
\downarrow\\
\text{direction}\\
\downarrow\\
\text{ROPE/equivalence}\\
\downarrow\\
\text{meaningful magnitude}\\
\downarrow\\
\text{model evidence if needed}\\
\downarrow\\
\text{decision with explicit criteria}
\end{array}
}
```

No single index replaces the whole sequence.

## References

Doorn, Johnny van, Don van den Bergh, Udo Böhm, et al. 2021. “The JASP
Guidelines for Conducting and Reporting a Bayesian Analysis.”
*Psychonomic Bulletin & Review* 28: 813–26.
<https://doi.org/10.3758/s13423-020-01798-5>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Makowski, Dominique, Mattan S. Ben-Shachar, S. H. Annabel Chen, and
Daniel Lüdecke. 2019. “Indices of Effect Existence and Significance in
the Bayesian Framework.” *Frontiers in Psychology* 10: 2767.
<https://doi.org/10.3389/fpsyg.2019.02767>.

Tendeiro, Jorge N., Rink Hoekstra, Tsz Keung Wong, and Henk A. L. Kiers.
2025. “Introduction to the Bayes Factor: A Shiny/r App.” *Teaching
Statistics* 47 (1): 5–16. <https://doi.org/10.1111/test.12380>.
