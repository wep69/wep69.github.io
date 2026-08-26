# Bayesian Split-Plot Experiments: Experimental Units, Hierarchy, and Treatment Comparisons

## 1. Why split plots deserve a separate workflow

A split-plot experiment contains more than one randomization stage.

A whole-plot treatment is randomized to relatively large experimental
units. A subplot treatment is then randomized within those whole plots.
The two treatments therefore do not have the same experimental error.

This is not merely an ANOVA bookkeeping detail. It is part of the data
generating structure.

The central rule of this vignette is:

**Identify the whole-plot experimental unit before writing the
statistical formula.**

Version 1.0.0 includes a Gaussian split-plot path specifically to ensure
that the package architecture can represent hierarchical experiments
from its first release.

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  distinguish blocks, whole plots, and subplots;
2.  identify the experimental unit for each treatment factor;
3.  recognize pseudoreplication in a naive flat analysis;
4.  construct a `bayes_split_plot` design object;
5.  audit whole-plot identifiers and subplot replication;
6.  understand the version 1.0.0 multilevel model;
7.  configure CmdStan through
    [`bayes_compute()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compute.md);
8.  specify regularizing priors and inspect prior predictions;
9.  fit the split-plot model through `brms`;
10. diagnose Stan sampling before interpreting effects;
11. compare whole-plot and subplot treatment effects;
12. interpret interactions without ignoring randomization hierarchy;
13. use posterior predictive checking;
14. compare the Bayesian model with the classical split-plot
    error-stratum representation for teaching;
15. understand which future extensions are already anticipated by the
    design object.

## 3. The teaching experiment

The bundled simulated dataset uses:

- four blocks;
- three irrigation levels as whole-plot treatments;
- three cultivars as subplot treatments;
- one whole plot for each irrigation level within each block;
- three subplots per whole plot.

``` r

d <- bayes_teaching_data("split_plot")
head(d)
#>   block irrigation whole_plot_id cultivar    yield
#> 1    B1        I60        B1_I60       C1 7.439078
#> 2    B1        I60        B1_I60       C2 7.036462
#> 3    B1        I60        B1_I60       C3 7.575950
#> 4    B1        I80        B1_I80       C1 7.637213
#> 5    B1        I80        B1_I80       C2 8.287738
#> 6    B1        I80        B1_I80       C3 7.883002
with(d, table(block, irrigation))
#>      irrigation
#> block I100 I60 I80
#>    B1    3   3   3
#>    B2    3   3   3
#>    B3    3   3   3
#>    B4    3   3   3
with(d, table(whole_plot_id, cultivar))
#>              cultivar
#> whole_plot_id C1 C2 C3
#>       B1_I100  1  1  1
#>       B1_I60   1  1  1
#>       B1_I80   1  1  1
#>       B2_I100  1  1  1
#>       B2_I60   1  1  1
#>       B2_I80   1  1  1
#>       B3_I100  1  1  1
#>       B3_I60   1  1  1
#>       B3_I80   1  1  1
#>       B4_I100  1  1  1
#>       B4_I60   1  1  1
#>       B4_I80   1  1  1
```

The dataset is simulated and is not field evidence.

## 4. Experimental units

Let irrigation be factor $`A`$ and cultivar be factor $`B`$.

The randomization occurs in two stages.

### Stage 1: whole plots

Within each block, irrigation treatments are assigned to whole plots.

The independent experimental units for irrigation are therefore the
whole plots, not the individual cultivar subplots.

### Stage 2: subplots

Within each whole plot, cultivar treatments are assigned to subplots.

The subplot experimental units provide replication for cultivar and for
the within-whole-plot component of the interaction.

A flat model that treats all subplot observations as independent
replicates of irrigation can understate uncertainty for the whole-plot
treatment.

## 5. Create an explicit split-plot design

``` r

des <- bayes_split_plot(
  data = d,
  response = "yield",
  block = "block",
  whole_plot = "irrigation",
  subplot = "cultivar",
  whole_plot_id = "whole_plot_id"
)

des
#> <bayes_design>
#>   Type: Split-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   block: block
#>   whole_plot: irrigation
#>   subplot: cultivar
#>   whole_plot_id: whole_plot_id
```

The `whole_plot_id` should uniquely identify the actual whole-plot
experimental unit across the complete dataset.

## 6. Audit hierarchy before modeling

``` r

audit <- bayes_design_audit(des)
audit
#> <bayes_design_audit>
#>   Design: Split-plot design
#>   Status: PASS
audit$checks$subplots_per_whole_plot
#> 
#> B1_I100  B1_I60  B1_I80 B2_I100  B2_I60  B2_I80 B3_I100  B3_I60  B3_I80 B4_I100 
#>       3       3       3       3       3       3       3       3       3       3 
#>  B4_I60  B4_I80 
#>       3       3
audit$checks$whole_plot_factor_per_block
#>     
#>      I100 I60 I80
#>   B1    1   1   1
#>   B2    1   1   1
#>   B3    1   1   1
#>   B4    1   1   1
```

Version 1.0.0 checks that:

- a whole-plot ID maps to one block and one whole-plot treatment;
- each whole plot contains the subplot levels;
- subplot counts are balanced in the teaching structure;
- each whole-plot treatment occurs once per block.

The audit is intentionally conservative. Future versions can relax the
balanced constraints when explicit incomplete or replicated split-plot
structures are implemented.

## 7. Why the native engine is not used here

The narrow native engine is a fixed-effect Gaussian teaching model. It
does not contain the separate whole-plot variance component needed for a
split-plot experiment.

``` r

bayes_plan(des)
#> <bayes_plan>
#>   Design: Split-plot design
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
bayes_capabilities("split_plot")
#>       design      engine             status estimation evidence hierarchical
#> 1 split_plot      native      not_supported      FALSE    FALSE        FALSE
#> 2 split_plot bayesfactor      not_supported      FALSE    FALSE        FALSE
#> 3 split_plot        brms supported_optional       TRUE     TRUE         TRUE
#> 4 split_plot        pymc      not_supported      FALSE    FALSE        FALSE
#>   non_gaussian flexible_response
#> 1        FALSE             FALSE
#> 2        FALSE             FALSE
#> 3        FALSE             FALSE
#> 4        FALSE             FALSE
#>                                                                         notes
#> 1                        The native engine does not represent this structure.
#> 2                           No validated BayesFactor adapter for this design.
#> 3                   Stan posterior model through brms with cmdstanr or rstan.
#> 4 PyMC is not a general replacement backend for this design in version 1.0.0.
```

The automatic route is therefore `brms`.

This is an example of capability-based routing. The package does not
select `brms` because it is a preferred brand. It selects the backend
because the design requires a multilevel structure that the native
engine does not provide.

## 8. Version 1.0.0 model

The hierarchical formula is

``` text
yield ~ irrigation * cultivar +
        (1 | block) +
        (1 | whole_plot_id)
```

A simplified model representation is

``` math
y_{ijkl}
=
\mu
+\alpha_i
+\beta_j
+(\alpha\beta)_{ij}
+u_k
+w_{ki}
+\varepsilon_{ijkl},
```

where

``` math
u_k\sim N(0,\sigma_b^2),
```

``` math
w_{ki}\sim N(0,\sigma_w^2),
```

and

``` math
\varepsilon_{ijkl}\sim N(0,\sigma^2).
```

The whole-plot random intercept $`w_{ki}`$ represents correlation among
subplots sharing the same whole plot.

## 9. Priors

The default package profile is intended for teaching and general
regularization.

``` r

pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

For a real confirmatory split-plot analysis, explicit priors should be
considered for:

- fixed treatment effects;
- residual SD;
- block SD;
- whole-plot SD.

The `brms_prior` argument can hold a custom `brms` prior specification.

Prior choices should be justified rather than hidden behind a software
default. Reporting guidelines emphasize explaining both the likelihood
and prior distributions (Kruschke 2021).

## 10. Prior predictive thinking

[`bayes_prior_predictive()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_prior_predictive.md)
provides a design-matrix teaching check, but the final split-plot prior
predictive analysis should preferably be performed through the exact
hierarchical `brms` model with `sample_prior = "only"` or with prior
draws from the fitted Stan program.

The important scientific questions are:

- Are predicted yields in a plausible range?
- Is the prior whole-plot heterogeneity plausible?
- Is the prior block heterogeneity plausible?
- Can the prior generate treatment differences much larger than any
  credible agronomic effect?

Version 1.0.0 keeps this distinction documented rather than pretending
that a fixed-design prior simulator fully represents the hierarchical
model.

## 11. Configure CmdStan

``` r

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 1000,
  seed = 20260824,
  adapt_delta = 0.95,
  max_treedepth = 12
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
```

The default recommendation for this small Gaussian teaching model is to
parallelize chains before considering within-chain threading.

## 12. Fit with brms and CmdStan

The heavy model fit is not executed while building this vignette.

``` r

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = pr,
  compute = cmp
)

fit
```

[`bayes_fit()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_fit.md)
records:

- design;
- audit plan;
- priors;
- backend;
- CPU configuration;
- chains;
- warmup;
- posterior sampling length;
- seed;
- model formula.

This provenance is part of the scientific result.

## 13. Diagnose first

``` r

diag <- bayes_diagnose(fit)
diag
```

For a Stan fit, inspect at least:

- R-hat;
- bulk ESS;
- tail ESS;
- MCSE;
- divergences;
- maximum tree-depth hits;
- E-BFMI if available.

A treatment contrast is not ready for scientific interpretation until
major sampling problems have been addressed.

Stan’s HMC/NUTS approach is valuable for correlated and hierarchical
posteriors, but its diagnostic information is an essential part of
fitting, not optional decoration (Annis et al. 2017).

## 14. Whole-plot treatment comparisons

Irrigation is randomized to whole plots.

``` r

irr_con <- bayes_contrasts(
  fit,
  factor = "irrigation",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

irr_con
```

The posterior uncertainty automatically includes the fitted whole-plot
variance component because the posterior predictions originate from the
hierarchical model.

This is the main reason not to replace the split-plot model with a flat
ordinary regression.

## 15. Subplot treatment comparisons

``` r

cult_con <- bayes_contrasts(
  fit,
  factor = "cultivar",
  meaningful = 0.30
)

cult_con
```

The cultivar comparison uses the same posterior model but a different
scientific factor.

## 16. Interaction

The interaction asks whether cultivar differences change across
irrigation levels.

Version 1.0.0 fits the interaction but exposes only marginal
one-factor-at-a-time posterior comparisons through
[`bayes_contrasts()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_contrasts.md).

For a strong interaction, the preferred scientific analysis is a
conditional simple-effect comparison such as cultivar within each
irrigation level. That interface is planned for a later release. Version
1.0.0 does not silently return such a conditional analysis when only a
marginal contrast was requested.

The fitted backend object remains available:

``` r

fit$backend_fit$fit
```

Advanced users can use `brms`, `emmeans`, or direct posterior prediction
for custom simple-effect calculations while the unified API is expanded.

## 17. Posterior predictive checking

``` r

ppc <- bayes_pp_check(fit, ndraws = 500)

ppc$observed
summary(ppc$replicated$mean)
summary(ppc$replicated$sd)
```

A useful split-plot PPC should go beyond the global response
distribution. Possible checks include:

- variance among block means;
- variance among whole plots;
- within-whole-plot residual spread;
- treatment-by-irrigation cell means;
- extreme yields;
- replicated interaction patterns.

Because
[`bayes_pp_check()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_pp_check.md)
retains posterior predictive draws, these design-specific summaries can
be constructed without refitting.

## 18. Classical split-plot crosswalk

``` r

freq <- bayes_compare_frequentist(fit)
freq$anova
```

The classical analysis uses an error-stratum representation based on
block and whole-plot treatment.

The pedagogical comparison is:

| Classical split plot | Bayesian multilevel split plot |
|----|----|
| separate error strata | explicit hierarchical variance components |
| F tests | posterior treatment distributions and optional model evidence |
| point/SE/CI | posterior distribution/CrI |
| no direct posterior probability | direct posterior probabilities for defined quantities |
| p-value | no one-to-one Bayesian replacement |

Do not describe the multilevel model as valid because it agrees with an
F test. Both are representations of the same experimental structure
under different inferential frameworks.

## 19. ROPE in split plots

The same practical-relevance principles apply.

``` r

bayes_equivalence(
  fit,
  range = c(-0.20, 0.20),
  probability = 0.95,
  factor = "irrigation"
)
```

However, the ROPE must be defined on the **contrast estimand**, not on a
random effect SD unless that variance component itself is the scientific
target.

## 20. Bayes factors for split plots

Version 1.0.0 does not automatically generate a split-plot BF by
deleting terms from the model.

Why?

- marginal likelihoods require proper priors;
- the full and reduced models need scientifically defensible
  definitions;
- treatment interactions complicate model hierarchy;
- numerical bridge sampling should be checked for stability;
- removing a whole-plot effect can change the interpretation of
  remaining terms.

If two explicit `brms` models have been fit with proper priors:

``` r

bf <- bayes_bf(full_fit, reduced_fit)
bf
```

the package delegates marginal-likelihood estimation to the `brms`
bridge sampling interface (Gronau et al. 2020).

## 21. Pseudoreplication example

Imagine three cultivar observations inside one irrigation whole plot.
They are three subplot observations, but only one whole-plot realization
of that irrigation treatment within that block.

Treating all three as independent irrigation replicates would count the
same whole-plot randomization multiple times.

The explicit `whole_plot_id` makes this dependence visible in both the
audit and hierarchical formula.

## 22. Missing or incomplete split plots

Version 1.0.0 targets a balanced teaching structure and rejects
structural audit failures before fitting.

Future releases can support:

- missing subplots;
- repeated whole-plot treatment levels within block;
- unequal subplot counts;
- incomplete split plots;
- split-split plots;
- spatially arranged whole plots.

The important architectural point is that the experimental-unit
identifiers are already stored independently of the backend.

## 23. Preparing for split-split plots

A split-split plot adds a third randomization level.

Conceptually:

``` text
block
  -> whole plot: factor A
       -> subplot: factor B
            -> sub-subplot: factor C
```

A future design schema can add `subplot_id` and `subsubplot` roles while
preserving the same top-level workflow:

``` text
design -> audit -> prior -> fit -> diagnostics -> contrasts
```

## 24. Preparing for repeated measures

Repeated measurements introduce a different type of dependence. Time is
not automatically a subplot factor.

Future models need to distinguish:

- experimental hierarchy created by randomization;
- repeated observations on the same unit;
- residual temporal correlation.

The current `future_roles` registry recognizes `subject` and `time` so
that this information can later be incorporated without redefining all
design objects.

## 25. Complete local workflow

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("split_plot")

des <- bayes_split_plot(
  d,
  response = "yield",
  block = "block",
  whole_plot = "irrigation",
  subplot = "cultivar",
  whole_plot_id = "whole_plot_id"
)

audit <- bayes_design_audit(des)
stopifnot(audit$status == "PASS")

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  seed = 20260824
)

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior(),
  compute = cmp
)

stopifnot(bayes_diagnose(fit)$status != "FAIL")

irr <- bayes_contrasts(
  fit,
  "irrigation",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

cult <- bayes_contrasts(
  fit,
  "cultivar",
  meaningful = 0.30
)

bayes_pp_check(fit, ndraws = 500)

bayes_report(
  fit,
  file = "split_plot_report.md",
  contrast_factor = "irrigation",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)
```

## 26. Reporting checklist

For a split-plot paper or thesis, report:

- experimental units at both randomization stages;
- block structure;
- whole-plot and subplot factors;
- number of whole plots and subplots;
- model formula;
- random/grouping effects;
- priors on fixed effects and variance components;
- prior predictive assessment;
- backend, CmdStan version, chains, iterations, seed, and CPUs;
- R-hat, ESS, divergences, tree depth, and energy diagnostic where
  available;
- posterior whole-plot treatment estimates;
- posterior subplot treatment estimates;
- interaction estimates;
- practical thresholds or ROPE with justification;
- posterior predictive assessment;
- prior sensitivity;
- any BF model comparison with explicit competing models and proper
  priors.

## References

Annis, Jeffrey, Brent J. Miller, and Thomas J. Palmeri. 2017. “Bayesian
Inference with Stan: A Tutorial on Adding Custom Distributions.”
*Behavior Research Methods* 49: 863–86.
<https://doi.org/10.3758/s13428-016-0746-9>.

Gronau, Quentin F., Henrik Singmann, and Eric-Jan Wagenmakers. 2020.
“Bridgesampling: An r Package for Estimating Normalizing Constants.”
*Journal of Statistical Software* 92 (10): 1–29.
<https://doi.org/10.18637/jss.v092.i10>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.
