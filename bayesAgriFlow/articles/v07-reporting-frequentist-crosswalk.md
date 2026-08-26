# Bayesian Reporting and the Frequentist Crosswalk

## 1. Purpose

Many users approach Bayesian analysis after years of training in ANOVA,
p-values, confidence intervals, and multiple-comparison procedures. A
useful teaching package should acknowledge that background without
pretending that Bayesian quantities are re-labeled frequentist
quantities.

This vignette develops two connected workflows:

1.  a **frequentist crosswalk** used only to connect familiar
    experimental design concepts to their Bayesian counterparts;
2.  a **Bayesian reporting workflow** aligned with recommendations from
    the JASP guidelines and Bayesian Analysis Reporting Guidelines
    (Doorn et al. 2021; Kruschke 2021).

## 2. Learning objectives

After this vignette, the reader should be able to:

1.  distinguish a p-value from a Bayes factor;
2.  distinguish a confidence interval from a credible interval;
3.  explain why failure to reject $`H_0`$ is not automatically evidence
    for $`H_0`$;
4.  use
    [`bayes_compare_frequentist()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compare_frequentist.md)
    without allowing frequentist output to determine Bayesian
    conclusions;
5.  report model structure, priors, prior predictive checks, and
    computational details;
6.  report posterior estimates and practical relevance;
7.  report BF with numerator/denominator models and priors;
8.  report MCMC diagnostics;
9.  report posterior predictive checks;
10. document sensitivity;
11. produce a reproducible Markdown report;
12. use
    [`bayes_report_check()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_report_check.md)
    as a completeness aid rather than a scientific quality score.

## 3. A familiar CRD example

``` r

d <- bayes_teaching_data("crd")
des <- bayes_crd(d, "yield", "treatment")
fit <- bayes_fit(des, draws = 2500)
```

The frequentist crosswalk is:

``` r

freq <- bayes_compare_frequentist(fit)
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

The Bayesian posterior summary is:

``` r

bayes_summary(fit)
#>          parameter       mean     median        sd       lower     upper
#> 1      (Intercept)  6.2645047  6.2635695 0.1378800  5.98842003 6.5451317
#> 2    treatmentBioB  0.4125206  0.4105219 0.1954306  0.03101114 0.8042218
#> 3    treatmentBioC  0.6903391  0.6900663 0.1916361  0.32304900 1.0886122
#> 4 treatmentControl -0.2114395 -0.2094518 0.1983001 -0.59856843 0.1815181
#> 5            sigma  0.3967813  0.3932114 0.0488550  0.31342509 0.5095793
#>   p_positive p_negative     pd
#> 1     1.0000     0.0000 1.0000
#> 2     0.9836     0.0164 0.9836
#> 3     0.9996     0.0004 0.9996
#> 4     0.1424     0.8576 0.8576
#> 5     1.0000     0.0000 1.0000
bayes_contrasts(
  fit,
  factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4125206 -0.4105219 0.1954306 -0.8042218
#> 2    BioA - BioC    BioA    BioC -0.6903391 -0.6900663 0.1916361 -1.0886122
#> 3 BioA - Control    BioA Control  0.2114395  0.2094518 0.1983001 -0.1815181
#> 4    BioB - BioC    BioB    BioC -0.2778185 -0.2749665 0.1953073 -0.6532676
#> 5 BioB - Control    BioB Control  0.6239601  0.6257813 0.2007429  0.2351679
#> 6 BioC - Control    BioC Control  0.9017786  0.9037138 0.1954717  0.5195427
#>         upper p_a_gt_b p_a_lt_b     pd rope_pct p_abs_gt_meaningful
#> 1 -0.03101114   0.0164   0.9836 0.9836   0.1320              0.5208
#> 2 -0.32304900   0.0004   0.9996 0.9996   0.0052              0.9412
#> 3  0.59856843   0.8576   0.1424 0.8576   0.4564              0.1668
#> 4  0.10574804   0.0708   0.9292 0.9292   0.3440              0.2612
#> 5  1.00646176   0.9996   0.0004 0.9996   0.0148              0.8660
#> 6  1.29239256   1.0000   0.0000 1.0000   0.0004              0.9944
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1          0.0000                0.5208
#> 2          0.0000                0.9412
#> 3          0.1648                0.0020
#> 4          0.0004                0.2608
#> 5          0.8660                0.0000
#> 6          0.9944                0.0000
```

These outputs can be placed side by side for teaching, but they should
not be translated term by term as if they were the same inferential
object.

## 4. p-value versus Bayes factor

A p-value is calculated under a null hypothesis and asks how unusual the
observed result or a more extreme result would be under that null model.

A Bayes factor compares two model predictions:

``` math
BF_{10}
=
\frac{p(y\mid H_1)}{p(y\mid H_0)}.
```

It can also be written as the update from prior to posterior odds:

``` math
\frac{P(H_1\mid y)}{P(H_0\mid y)}
=
BF_{10}
\frac{P(H_1)}{P(H_0)}.
```

The BF is therefore not

``` math
P(H_1\mid y)
```

unless additional prior-model information and a two-model normalization
are introduced.

The Bayes-factor teaching article supplied for this project emphasizes
this distinction and warns against treating BF as a Bayesian p-value
(Tendeiro et al. 2025).

## 5. Failure to reject is not evidence for absence

A large p-value does not, by itself, establish that the null hypothesis
is supported. It can reflect low information.

Bayesian model comparison can quantify evidence in favor of a specified
null model relative to a specified alternative, but that evidence
remains conditional on the model definitions and priors.

ROPE analysis provides another route by asking how much posterior mass
lies in a scientifically negligible region.

The two Bayesian routes are not identical:

``` text
BF:
relative evidence between full probability models

ROPE:
posterior probability assigned to a region of a parameter scale
```

## 6. Confidence interval versus credible interval

A frequentist confidence interval is generated by a procedure with
repeated sampling coverage properties.

A Bayesian credible interval summarizes posterior probability
conditional on the model, prior, and observed data.

`bayesAgriFlow` version 1.0.0 reports equal-tailed posterior intervals.

A sentence such as

> There is 95% posterior probability that the parameter lies within this
> reported 95% credible interval

is meaningful in the Bayesian model.

The same sentence should not be automatically applied to a frequentist
95% confidence interval.

## 7. ANOVA F test versus posterior effects

In an ANOVA, an F statistic compares variation attributable to a term
with an error quantity defined by the model and design.

In `bayesAgriFlow`, posterior treatment effects are represented directly
by draws from the model.

This allows questions such as:

``` math
P(\mu_A-\mu_B>0\mid y)
```

or

``` math
P(|\mu_A-\mu_B|>\delta\mid y).
```

There is no need to invent a “Bayesian F test” for every posterior
question.

## 8. Multiple comparisons

A classical workflow may use Tukey-adjusted pairwise tests.

A Bayesian workflow can retain the joint posterior and directly compute
posterior treatment contrasts.

``` r

bayes_contrasts(
  fit,
  factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4125206 -0.4105219 0.1954306 -0.8042218
#> 2    BioA - BioC    BioA    BioC -0.6903391 -0.6900663 0.1916361 -1.0886122
#> 3 BioA - Control    BioA Control  0.2114395  0.2094518 0.1983001 -0.1815181
#> 4    BioB - BioC    BioB    BioC -0.2778185 -0.2749665 0.1953073 -0.6532676
#> 5 BioB - Control    BioB Control  0.6239601  0.6257813 0.2007429  0.2351679
#> 6 BioC - Control    BioC Control  0.9017786  0.9037138 0.1954717  0.5195427
#>         upper p_a_gt_b p_a_lt_b     pd rope_pct p_abs_gt_meaningful
#> 1 -0.03101114   0.0164   0.9836 0.9836   0.1320              0.5208
#> 2 -0.32304900   0.0004   0.9996 0.9996   0.0052              0.9412
#> 3  0.59856843   0.8576   0.1424 0.8576   0.4564              0.1668
#> 4  0.10574804   0.0708   0.9292 0.9292   0.3440              0.2612
#> 5  1.00646176   0.9996   0.0004 0.9996   0.0148              0.8660
#> 6  1.29239256   1.0000   0.0000 1.0000   0.0004              0.9944
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1          0.0000                0.5208
#> 2          0.0000                0.9412
#> 3          0.1648                0.0020
#> 4          0.0004                0.2608
#> 5          0.8660                0.0000
#> 6          0.9944                0.0000
```

This table can report magnitude, uncertainty, direction, equivalence,
and meaningful-effect probability simultaneously.

Version 1.0.0 intentionally does not convert these probabilities into
compact letters automatically because a grouping rule must first be
defined.

## 9. Reporting starts before the Results section

The JASP guidelines organize Bayesian work into planning, execution,
interpretation, and reporting (Doorn et al. 2021).

A sound report should therefore be reconstructible from the research
question forward, not written only after seeing the posterior.

Important planning elements include:

- goal: estimation, testing, prediction, or a combination;
- directional or nondirectional scientific hypotheses;
- likelihood;
- priors;
- prior sensitivity plan;
- model checks;
- sample/experimental design;
- exploratory versus confirmatory status.

## 10. BARG structure

BARG organizes essential reporting around a sequence that can be
summarized as:

1.  explain why Bayesian analysis is appropriate and state the goal;
2.  explain the model and priors;
3.  report computational details;
4.  describe the posterior and posterior predictive adequacy;
5.  explain any decisions and their criteria;
6.  report sensitivity;
7.  make the analysis reproducible.

The package report functions are designed around this logic rather than
a single “significance” table.

## 11. Model specification

A report should name:

- dependent variable;
- predictors;
- experimental roles;
- likelihood;
- parameters of principal scientific interest;
- nuisance or grouping effects;
- prior distributions.

For example, a split-plot report should explicitly state that whole-plot
experimental units receive irrigation treatments and that subplots
within those units receive cultivar treatments.

The model hierarchy is scientific information.

## 12. Prior reporting

Do not write only:

> Default priors were used.

Report the actual prior distributions and explain why their scale is
reasonable.

If a default prior is used for a Bayes factor, report the relevant scale
and perform sensitivity analysis over plausible alternatives.

Bayes factors can be especially sensitive to prior width because the
marginal likelihood averages predictive performance across the prior
(Held and Ott 2018; Tendeiro et al. 2025).

## 13. Prior predictive reporting

A concise report can say:

> Prior predictive simulation was used to verify that the specified
> priors generated responses over a scientifically plausible range.

Then show a figure or summary in supplementary material.

The purpose is to demonstrate the implications of priors on the data
scale.

## 14. Computational reporting

For a Stan fit, report:

``` text
backend
Stan/CmdStan version
chains
warmup
post-warmup draws
seed
parallel chains
threads per chain
adapt_delta
max_treedepth
```

Then report the sampler diagnostics.

The absence of a warning printed to the console is not sufficient
documentation.

## 15. Posterior reporting

For continuous quantities, report at least:

- an explicit estimand;
- posterior location;
- credible interval;
- units.

Add direction probability only when it clarifies the scientific
question.

For treatment comparisons, report the contrast direction exactly as
printed.

## 16. Decision reporting

If a ROPE is used, report:

- lower and upper limits;
- scientific justification;
- posterior mass required by the decision rule.

If a meaningful-effect threshold is used, report:

- the threshold;
- units;
- why exceeding it matters.

If a BF decision rule is used, report:

- the BF itself;
- the models;
- priors;
- prior model probabilities if posterior model probabilities are used;
- the decision threshold and why that threshold was selected.

BARG explicitly requires decision criteria to be stated (Kruschke 2021).

## 17. Sensitivity reporting

Sensitivity analysis should be tied to uncertainty in modeling
decisions.

Examples include:

- alternative plausible prior widths;
- alternative scientifically defensible ROPE limits;
- alternative likelihoods in future versions;
- alternative random-effect structures;
- bridge-sampling repeatability;
- influential observations when scientifically justified.

Do not delete observations or alter priors merely to obtain a preferred
result.

## 18. Posterior predictive reporting

The report should show whether the model reproduces important data
features.

For a treatment experiment:

- central tendency by treatment;
- dispersion by treatment;
- extreme values.

For a split plot:

- whole-plot variation;
- within-whole-plot variation;
- treatment-by-subplot patterns.

A posterior predictive check is not a p-value replacement.

## 19. Reproducibility

A reproducible analysis should preserve:

- source data or an appropriate reproducible substitute;
- scripts;
- package versions;
- R version;
- backend version;
- random seed;
- computational settings;
- model code or formula;
- priors;
- precomputed MCMC objects for expensive analyses when feasible.

The package’s `LOCAL_VALIDATION.md` applies the same principle to
package release artifacts.

## 20. Generate a report

``` r

txt <- bayes_report(
  fit,
  contrast_factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

head(txt, 30)
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
#> [21] ""                                                                                                                                                                                              
#> [22] "| parameter | mean | median | sd | lower | upper | p_positive | p_negative | pd |"                                                                                                             
#> [23] "| --- | --- | --- | --- | --- | --- | --- | --- | --- |"                                                                                                                                       
#> [24] "| (Intercept) |  6.2650 |  6.2640 | 0.13790 |  5.98800 | 6.5450 | 1.0000 | 0.0000 | 1.0000 |"                                                                                                  
#> [25] "| treatmentBioB |  0.4125 |  0.4105 | 0.19540 |  0.03101 | 0.8042 | 0.9836 | 0.0164 | 0.9836 |"                                                                                                
#> [26] "| treatmentBioC |  0.6903 |  0.6901 | 0.19160 |  0.32300 | 1.0890 | 0.9996 | 0.0004 | 0.9996 |"                                                                                                
#> [27] "| treatmentControl | -0.2114 | -0.2095 | 0.19830 | -0.59860 | 0.1815 | 0.1424 | 0.8576 | 0.8576 |"                                                                                             
#> [28] "| sigma |  0.3968 |  0.3932 | 0.04885 |  0.31340 | 0.5096 | 1.0000 | 0.0000 | 1.0000 |"                                                                                                        
#> [29] ""                                                                                                                                                                                              
#> [30] "## Treatment contrasts"
```

Write it to disk with:

``` r

bayes_report(
  fit,
  file = "bayesian_crd_report.md",
  contrast_factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)
```

The generated report is a starting point, not a substitute for
scientific writing.

## 21. Audit report completeness

``` r

bayes_report_check(
  fit,
  rope_supplied = TRUE,
  sensitivity_done = FALSE,
  ppc_done = FALSE
)
#>                                       item status
#> 1           Design and variables specified   TRUE
#> 2        Likelihood/model formula recorded   TRUE
#> 3                           Prior recorded   TRUE
#> 4             Computation/backend recorded   TRUE
#> 5             Posterior interval available   TRUE
#> 6   Sampler/numerical diagnostics reviewed   TRUE
#> 7      Posterior predictive check reviewed  FALSE
#> 8               Prior sensitivity reviewed  FALSE
#> 9  ROPE justified when used for a decision   TRUE
#> 10                           Seed recorded   TRUE
```

After prior sensitivity and posterior predictive checks:

``` r

bayes_report_check(
  fit,
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

A `TRUE` entry means the workflow recorded that component. It does not
certify that the component was scientifically well chosen.

## 22. Reporting BF

``` r

if (requireNamespace("BayesFactor", quietly = TRUE)) {
  bf_fit <- bayes_fit(des, engine = "bayesfactor")
  ev <- bayes_evidence(bf_fit)
  ev
}
```

A suitable sentence identifies the orientation, for example:

> The reported $`BF_{10}`$ compares the specified treatment model with
> the denominator model under the BayesFactor prior specification.

Then report the numeric value.

Avoid writing:

> The probability that the alternative is true is BF10.

That statement is incorrect.

## 23. Suggested Results architecture

A concise scientific Results section can follow this order:

### Posterior estimation

Report treatment estimates and uncertainty.

### Planned comparisons

Report contrasts and direction.

### Practical relevance

Report ROPE and meaningful-threshold probabilities where pre-specified.

### Model evidence

Report BF only if hypothesis/model evidence was part of the goal.

### Model adequacy

Report posterior predictive findings.

### Sensitivity

Report whether conclusions changed under plausible alternative priors or
decision limits.

This structure prevents a single index from dominating the entire
analysis.

## 24. Suggested Methods architecture

Methods can follow:

1.  experimental design and experimental unit;
2.  response model;
3.  priors and prior predictive assessment;
4.  computational backend and settings;
5.  posterior summaries;
6.  contrast definitions;
7.  ROPE/meaningful thresholds;
8.  Bayes-factor models when used;
9.  posterior predictive checks;
10. sensitivity analyses;
11. reproducibility information.

## 25. A compact crosswalk table

| Frequentist concept | Bayesian analogue or distinction |
|----|----|
| treatment estimate | posterior treatment estimand |
| standard error | posterior SD and Monte Carlo error are different quantities |
| 95% confidence interval | 95% credible interval, different interpretation |
| p-value | no universal Bayesian equivalent |
| failure to reject $`H_0`$ | not automatically evidence for $`H_0`$ |
| F test | posterior effects and/or explicit model comparison |
| Tukey pairwise test | posterior planned/pairwise contrasts |
| “nonsignificant” | may correspond to uncertain, practically equivalent, or poorly informed posterior states |
| point optimum | posterior distribution of the constrained optimum |
| model selection by p | BF, PSIS-LOO, predictive weighting, BMA, or projection-predictive comparison according to the scientific target |

## 26. Final rule

The purpose of the frequentist crosswalk is to help the learner orient
themselves.

The purpose of the Bayesian analysis is to answer Bayesian scientific
questions on their own terms.

## References

Doorn, Johnny van, Don van den Bergh, Udo Böhm, et al. 2021. “The JASP
Guidelines for Conducting and Reporting a Bayesian Analysis.”
*Psychonomic Bulletin & Review* 28: 813–26.
<https://doi.org/10.3758/s13423-020-01798-5>.

Held, Leonhard, and Manuela Ott. 2018. “On p-Values and Bayes Factors.”
*Annual Review of Statistics and Its Application* 5: 393–419.
<https://doi.org/10.1146/annurev-statistics-031017-100307>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Tendeiro, Jorge N., Rink Hoekstra, Tsz Keung Wong, and Henk A. L. Kiers.
2025. “Introduction to the Bayes Factor: A Shiny/r App.” *Teaching
Statistics* 47 (1): 5–16. <https://doi.org/10.1111/test.12380>.
