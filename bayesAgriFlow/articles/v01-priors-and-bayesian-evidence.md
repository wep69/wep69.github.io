# Priors, Bayes Factors, and Bayesian Evidence

## 1. Purpose

This vignette develops the evidence layer of `bayesAgriFlow`. It
deliberately keeps posterior estimation and model evidence separate.

A posterior distribution answers questions such as

``` math
P(\beta > 0\mid y)
```

or

``` math
P(|\Delta|>\delta_{\min}\mid y).
```

A Bayes factor answers a different question. For two fully specified
models,

``` math
BF_{10}
=
\frac{p(y\mid H_1)}{p(y\mid H_0)}.
```

It measures how much more strongly the observed data are predicted by
one model than by the other, after averaging the likelihood over each
model’s parameter prior. This distinction is emphasized in the supplied
Bayes-factor teaching article (Tendeiro et al. 2025) and in Bayesian
analysis guidelines (Doorn et al. 2021).

## 2. Learning objectives

After this vignette, the reader should be able to:

1.  distinguish a parameter prior from a prior model probability;
2.  explain why a Bayes factor depends on the priors within competing
    models;
3.  distinguish BF, prior odds, posterior odds, and posterior model
    probability;
4.  use prior predictive simulation before posterior fitting;
5.  assess prior-scale sensitivity for the native engine;
6.  obtain model-space evidence from the optional `BayesFactor` adapter;
7.  understand the assumptions behind the package’s inclusion-BF
    teaching summary;
8.  avoid describing BF as effect magnitude;
9.  understand why bridge sampling is restricted to explicitly
    comparable Stan models with proper priors;
10. report evidence without hiding inconclusive results.

## 3. Three different prior concepts

### 3.1 Parameter priors

For a Gaussian model,

``` math
y_i \sim N(\mu_i,\sigma^2),
\qquad
\mu_i=\mathbf x_i^\mathsf T\boldsymbol\beta,
```

a parameter prior assigns probability to possible values of
$`\boldsymbol\beta`$ and $`\sigma`$.

``` r

p_regular <- bayes_prior("regularizing")
p_skeptical <- bayes_prior("skeptical")
p_weak <- bayes_prior("weak")

p_regular
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
p_skeptical
#> <bayes_prior>
#>   Strategy: skeptical
#>   Coefficient scale: 1
#>   Intercept scale: 3
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
p_weak
#> <bayes_prior>
#>   Strategy: weak
#>   Coefficient scale: 5
#>   Intercept scale: 10
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

These profiles are conveniences for teaching. They do not remove the
responsibility to justify priors for a real study.

### 3.2 Prior model probabilities

A prior model probability concerns a discrete model or hypothesis, for
example

``` math
P(H_0)=0.5,\qquad P(H_1)=0.5.
```

The corresponding prior odds are

``` math
\frac{P(H_1)}{P(H_0)}=1.
```

This is not the same object as the prior distribution of an effect size
within $`H_1`$.

### 3.3 Prior predictive distributions

The prior predictive distribution integrates over parameter uncertainty:

``` math
p(\tilde y)
=
\int p(\tilde y\mid\theta)p(\theta)\,d\theta.
```

It translates prior assumptions into the units of future observations.

## 4. Prior predictive checking

``` r

d <- bayes_teaching_data("crd")
des <- bayes_crd(d, "yield", "treatment")

pp_regular <- bayes_prior_predictive(
  des,
  p_regular,
  draws = 300,
  seed = 20260824
)

pp_regular$summary
#>   statistic       value
#> 1   minimum -14.8951140
#> 2    median   6.1694196
#> 3   maximum  19.3705258
#> 4   mean_sd   0.9350504
```

Ask whether the simulated values are plausible before looking at
posterior conclusions. A prior can appear mathematically broad while
generating values that are physically impossible for a particular
response scale. BARG therefore treats prior predictive checking as an
important reporting item (Kruschke 2021).

## 5. Why extremely vague priors can matter for Bayes factors

For a composite alternative,

``` math
p(y\mid H_1)
=
\int p(y\mid\theta,H_1)p(\theta\mid H_1)\,d\theta.
```

The prior under $`H_1`$ is inside the integral. Spreading mass over
implausibly large parameter values can lower the average predictive
density at the observed data. Therefore, a prior that is described as
“noninformative” for parameter estimation need not behave neutrally in a
point-null Bayes-factor comparison (Tendeiro et al. 2025; Held and Ott
2018).

This is one reason `bayesAgriFlow` never prints a BF without keeping the
evidence backend and prior note available.

## 6. Fit for posterior estimation first

``` r

fit <- bayes_fit(
  des,
  engine = "native",
  prior = p_regular,
  draws = 2000,
  seed = 20260824
)

bayes_effects(fit)
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

This posterior fit answers magnitude and direction questions. It does
not automatically create a Bayes factor.

## 7. Prior sensitivity for estimation

``` r

sens <- bayes_prior_sensitivity(
  des,
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

Compare posterior medians and intervals across scientifically plausible
choices. Sensitivity is not a ritual in which every possible prior is
tried. It should focus on alternative priors that a knowledgeable
analyst could have defended before seeing the final result.

## 8. Evidence with BayesFactor

The following code is executed only when the optional package is
installed.

``` r

fit_bf <- bayes_fit(
  des,
  engine = "bayesfactor"
)

ev <- bayes_evidence(fit_bf)
ev
```

The `BF` column is evidence relative to the denominator model chosen by
the backend.

If prior odds are supplied,

``` r

bayes_evidence(fit_bf, prior_odds = 0.5)
```

the package additionally calculates

``` math
\text{posterior odds}_{10}
=
BF_{10}\times\text{prior odds}_{10}.
```

For a two-model comparison, posterior probability can then be obtained
from

``` math
P(H_1\mid y)
=
\frac{\text{posterior odds}_{10}}
{1+\text{posterior odds}_{10}}.
```

The BF itself has not changed. Prior odds and BF are separate terms.

## 9. Evidence after a native fit

A user can request

``` r

ev2 <- bayes_evidence(fit)
ev2$prior_note
```

This is convenient, but the package explicitly records that the evidence
analysis was fit separately with `BayesFactor` priors. It would be
misleading to claim that this BF arose from the native conjugate
posterior prior.

This separation is intentional. A unified API should not imply unified
priors when the underlying backends use different model specifications.

## 10. Inclusion Bayes factors

``` r

bayes_inclusion_bf(fit_bf)
```

Version 1.0.0 calculates a teaching-oriented inclusion BF over the
enumerated `BayesFactor` model space under equal prior model
probability.

For an effect $`E`$,

``` math
BF_{\mathrm{incl}}
=
\frac{
P(E\ \mathrm{included}\mid y)/
P(E\ \mathrm{excluded}\mid y)
}{
P(E\ \mathrm{included})/
P(E\ \mathrm{excluded})
}.
```

This can summarize model-space evidence for a term, but it inherits the
model set. Changing the candidate model set can change inclusion
evidence.

For confirmatory science, define the scientifically plausible model set
rather than enumerating every combinatorial possibility.

## 11. BF close to one

A BF near one means that the specified models predicted the observed
data similarly. It should not be described as proof that both hypotheses
are true, nor as support for the alternative, nor as proof that the
effect is absent.

The important distinction is:

- **evidence of absence** requires a model that represents a
  scientifically meaningful absence and sufficient evidence in its
  favor;
- **absence of evidence** can occur because the data are not diagnostic
  enough.

This distinction is a recurring theme in the JASP guidelines (Doorn et
al. 2021).

## 12. BF is not effect size

Suppose one treatment contrast has posterior median $`0.8`$ yield units.
A BF could be small, moderate, or large depending on sample size, noise,
prior width, and competing models. Conversely, a large BF can occur for
a small but precisely estimated effect.

Therefore, report both if both are scientifically relevant:

``` text
posterior contrast + credible interval + practical threshold
and
Bayes factor for the explicit model/hypothesis comparison
```

Do not replace one with the other.

## 13. Bridge sampling for Stan models

For complex `brms` models, version 1.0.0 exposes explicit two-model
comparison:

``` r

bf <- bayes_bf(full_fit, reduced_fit)
bf
```

The package fits `brms` models with `save_pars(all = TRUE)` because
marginal likelihood estimation needs the normalized model and all
required parameters. Bridge sampling estimates normalizing constants
numerically (Gronau et al. 2020).

Three rules apply.

1.  Competing models must represent a scientifically meaningful
    comparison.
2.  Priors used in marginal likelihoods must be proper.
3.  Numerical stability should be checked by repeating bridge sampling
    when the result is near a consequential decision boundary.

Version 1.0.0 does not automatically manufacture a full versus reduced
split-plot BF. The analyst must specify the competing models.

## 14. Evidence and posterior estimation can disagree without contradiction

Consider a posterior distribution concentrated on a small positive
value. The posterior can make $`P(\beta>0\mid y)`$ high while a model
comparison still finds that a point-null or practically null model
predicts the data competitively.

This is not a software inconsistency. “Which direction is most
plausible?” and “Which discrete model predicts these data better under
its prior?” are different questions.

The package therefore presents four distinct inferential layers:

| Layer               | Question                                        |
|---------------------|-------------------------------------------------|
| Direction           | Is the effect probably above or below zero?     |
| Magnitude           | How large is it, with uncertainty?              |
| Practical relevance | Is it large enough to matter?                   |
| Model evidence      | Which specified model predicts the data better? |

## 15. Reporting template

When a BF is included in a report, state:

1.  the numerator and denominator models;
2.  whether `BF10` or `BF01` is reported;
3.  the priors within each model;
4.  prior model probabilities if posterior model probabilities are
    reported;
5.  how the BF was computed;
6.  numerical integration error or stability information when available;
7.  prior sensitivity;
8.  posterior estimates of scientifically important effects.

BARG explicitly recommends reporting estimates even when a decision
about a null value is made (Kruschke 2021).

## 16. Recommended workflow

``` r

# Design
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")

# Estimation
fit <- bayes_fit(
  des,
  engine = "native",
  prior = bayes_prior("regularizing"),
  draws = 4000
)

bayes_effects(fit)
bayes_contrasts(fit, "treatment", meaningful = 0.4)

# Prior sensitivity
bayes_prior_sensitivity(des, c(1, 2.5, 5))

# Optional model evidence
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  ev <- bayes_evidence(fit)
  print(ev)
}
```

## 17. What version 1.0.0 intentionally does not do

The package does not:

- derive a BF from an arbitrary p-value;
- label any BF threshold as a universal scientific decision rule;
- treat a BF as effect size;
- silently convert prior odds into equal odds;
- apply Savage-Dickey density ratios indiscriminately;
- create a BF for flexible models when predictive comparison is more
  appropriate;
- claim that an automatic model set represents a pre-registered
  scientific hypothesis space.

Future versions can add informative equality/order hypotheses through
optional backends, but the same separation between evidence and
estimation will remain.

## References

Doorn, Johnny van, Don van den Bergh, Udo Böhm, et al. 2021. “The JASP
Guidelines for Conducting and Reporting a Bayesian Analysis.”
*Psychonomic Bulletin & Review* 28: 813–26.
<https://doi.org/10.3758/s13423-020-01798-5>.

Gronau, Quentin F., Henrik Singmann, and Eric-Jan Wagenmakers. 2020.
“Bridgesampling: An r Package for Estimating Normalizing Constants.”
*Journal of Statistical Software* 92 (10): 1–29.
<https://doi.org/10.18637/jss.v092.i10>.

Held, Leonhard, and Manuela Ott. 2018. “On p-Values and Bayes Factors.”
*Annual Review of Statistics and Its Application* 5: 393–419.
<https://doi.org/10.1146/annurev-statistics-031017-100307>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Tendeiro, Jorge N., Rink Hoekstra, Tsz Keung Wong, and Henk A. L. Kiers.
2025. “Introduction to the Bayes Factor: A Shiny/r App.” *Teaching
Statistics* 47 (1): 5–16. <https://doi.org/10.1111/test.12380>.
