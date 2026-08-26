# Bayesian Split-Split-Plot and Strip-Plot Experiments

## 1. Why randomization stages must remain visible

Complex field designs are easiest to misanalyze when the statistical
formula is written before the randomization is understood. A
split-split-plot contains three treatment-assignment scales. A strip
plot contains two treatment strips whose intersections form
observational combinations. Neither design can be safely reduced to an
ordinary factorial model with one undifferentiated residual term.

`bayesAgriFlow` therefore requires identifiers for the experimental
units that correspond to the randomization stages. The package does not
infer those IDs silently from treatment labels.

## 2. Learning objectives

After this vignette, the reader should be able to:

1.  reconstruct the randomization hierarchy of a split-split plot;
2.  distinguish whole plots, subplots, and sub-subplots;
3.  explain why different treatment factors have different
    experimental-unit scales;
4.  specify explicit whole-plot and subplot identifiers;
5.  audit the split-split hierarchy before fitting;
6.  reconstruct the crossed-strip randomization of a strip plot;
7.  distinguish strip experimental units from their intersections;
8.  inspect the hierarchical model compiled for each design;
9.  interpret variance components at the correct experimental scale;
10. fit the model using `brms` and `cmdstanr`;
11. use posterior contrasts without ignoring the design hierarchy;
12. use the frequentist error-stratum model only as a teaching
    crosswalk;
13. understand why automatic Bayes factors are deliberately limited for
    these designs;
14. document the design in a reproducible scientific report.

## 3. Split-split-plot teaching example

``` r

dss <- bayes_teaching_data("split_split")
head(dss)
#>   block irrigation cultivar bioinput whole_plot_id   subplot_id    yield
#> 1    B1        Low       C1       No        B1_Low    B1_Low_C1 4.825777
#> 2    B1        Low       C1      Yes        B1_Low    B1_Low_C1 4.648820
#> 3    B1        Low       C2       No        B1_Low    B1_Low_C2 4.958251
#> 4    B1        Low       C2      Yes        B1_Low    B1_Low_C2 5.193696
#> 5    B1     Medium       C1       No     B1_Medium B1_Medium_C1 5.741672
#> 6    B1     Medium       C1      Yes     B1_Medium B1_Medium_C1 6.431787
dim(dss)
#> [1] 48  7
with(dss, table(block, irrigation, cultivar, bioinput))
#> , , cultivar = C1, bioinput = No
#> 
#>      irrigation
#> block High Low Medium
#>    B1    1   1      1
#>    B2    1   1      1
#>    B3    1   1      1
#>    B4    1   1      1
#> 
#> , , cultivar = C2, bioinput = No
#> 
#>      irrigation
#> block High Low Medium
#>    B1    1   1      1
#>    B2    1   1      1
#>    B3    1   1      1
#>    B4    1   1      1
#> 
#> , , cultivar = C1, bioinput = Yes
#> 
#>      irrigation
#> block High Low Medium
#>    B1    1   1      1
#>    B2    1   1      1
#>    B3    1   1      1
#>    B4    1   1      1
#> 
#> , , cultivar = C2, bioinput = Yes
#> 
#>      irrigation
#> block High Low Medium
#>    B1    1   1      1
#>    B2    1   1      1
#>    B3    1   1      1
#>    B4    1   1      1
```

The teaching experiment has:

- blocks;
- irrigation assigned to whole plots;
- cultivar assigned to subplots;
- bioinput assigned to sub-subplots.

The data are simulated and frozen for teaching.

## 4. Randomization hierarchy

The hierarchy can be sketched as:

``` text
Block
  |
  +-- Whole plot: irrigation
        |
        +-- Subplot: cultivar
              |
              +-- Sub-subplot: bioinput
```

The same hierarchy should be visible in the data table through explicit
identifiers.

``` r

head(dss[c("block", "whole_plot_id", "subplot_id",
           "irrigation", "cultivar", "bioinput")])
#>   block whole_plot_id   subplot_id irrigation cultivar bioinput
#> 1    B1        B1_Low    B1_Low_C1        Low       C1       No
#> 2    B1        B1_Low    B1_Low_C1        Low       C1      Yes
#> 3    B1        B1_Low    B1_Low_C2        Low       C2       No
#> 4    B1        B1_Low    B1_Low_C2        Low       C2      Yes
#> 5    B1     B1_Medium B1_Medium_C1     Medium       C1       No
#> 6    B1     B1_Medium B1_Medium_C1     Medium       C1      Yes
length(unique(dss$whole_plot_id))
#> [1] 12
length(unique(dss$subplot_id))
#> [1] 24
```

## 5. Construct the split-split design

``` r

des_ss <- bayes_split_split_plot(
  data = dss,
  response = "yield",
  block = "block",
  whole_plot = "irrigation",
  subplot = "cultivar",
  subsubplot = "bioinput",
  whole_plot_id = "whole_plot_id",
  subplot_id = "subplot_id"
)

des_ss
#> <bayes_design>
#>   Type: Split-split-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 48
#>   block: block
#>   whole_plot: irrigation
#>   subplot: cultivar
#>   whole_plot_id: whole_plot_id
#>   subsubplot: bioinput
#>   subplot_id: subplot_id
```

The explicit IDs protect against a common mistake: treating every row as
an independent replicate for the irrigation effect.

## 6. Audit before fitting

``` r

aud_ss <- bayes_design_audit(des_ss)
aud_ss
#> <bayes_design_audit>
#>   Design: Split-split-plot design
#>   Status: PASS
aud_ss$checks$whole_plot_ids_unique_to_block_and_A
#> [1] TRUE
aud_ss$checks$subplot_ids_unique_to_whole_plot_and_B
#> [1] TRUE
```

The audit is structural. It checks whether the identifiers behave
consistently with the declared design. It does not prove that the
original field randomization was performed correctly.

## 7. Hierarchical model representation

The version 1.0.0 split-split compiler uses a Gaussian hierarchical
structure that is conceptually equivalent to:

``` text
yield ~ irrigation * cultivar * bioinput +
        (1 | block) +
        (1 | whole_plot_id) +
        (1 | subplot_id)
```

The residual distribution represents variation at the lowest modeled
sub-subplot scale.

This organization keeps distinct sources of variability for block,
whole-plot experimental units, subplot experimental units, and residual
observations.

## 8. Why the treatment effects do not share one error scale

Irrigation was randomized to a relatively large whole plot. Its
information is therefore tied to the number and variability of
independent whole plots. Cultivar was randomized within whole plots and
has a different replication scale. Bioinput was randomized at the
smallest experimental scale.

A Bayesian hierarchical model does not need to reproduce a classical
ANOVA error table literally, but it must still honor these
experimental-unit levels.

## 9. Priors for hierarchical variance components

``` r

pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

A prior for a whole-plot SD expresses prior uncertainty about variation
among whole plots after accounting for modeled fixed effects. It is not
a prior on the irrigation treatment effect itself.

Prior predictive reasoning should ask whether the combined fixed and
hierarchical priors generate plausible yields and plausible differences
among randomization units.

## 10. CmdStanR fit

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

fit_ss <- bayes_fit(
  des_ss,
  engine = "brms",
  prior = pr,
  compute = ctrl
)
```

The model is routed to `brms` because the native teaching engine does
not pretend that a fixed-effects Gaussian regression reproduces this
hierarchy.

## 11. Diagnostics

``` r

bayes_diagnose(fit_ss)
bayes_pp_check(fit_ss)
```

Hierarchical models can produce difficult posterior geometries,
particularly when variance components are weakly informed. Divergences
or poor effective sample size should be resolved before treatment
conclusions are reported.

## 12. Variance components

``` r

bayes_variance_components(fit_ss)
```

Interpret each variance component in terms of its experimental scale:

- block-to-block variability;
- whole-plot variability within the modeled design;
- subplot variability;
- residual sub-subplot variability.

The posterior intervals are often more informative than a binary
statement that a variance component is or is not different from zero.

## 13. Factorial treatment structure inside the hierarchy

The fixed treatment component includes:

``` math
A + B + C + AB + AC + BC + ABC.
```

For the teaching data:

- $`A`$ is irrigation;
- $`B`$ is cultivar;
- $`C`$ is bioinput.

A three-way interaction means that a two-factor interaction itself
changes across levels of the third factor. It should not be summarized
by a single main-effect sentence.

## 14. Posterior contrasts

``` r

bayes_contrasts(
  fit_ss,
  factor = "irrigation",
  rope = c(-0.20, 0.20),
  meaningful = 0.50
)
```

The numerical ROPE above is only illustrative. A real analysis must
justify the interval in response units.

When interactions are important, marginal contrasts should be
supplemented by conditional comparisons or response-scale predictions
for scientifically relevant combinations.

## 15. Response-scale predictions

``` r

nd_ss <- expand.grid(
  irrigation = levels(dss$irrigation),
  cultivar = levels(dss$cultivar),
  bioinput = levels(dss$bioinput)
)

pred_ss <- bayes_predict(
  fit_ss,
  newdata = nd_ss,
  type = "mean",
  re_formula = NA
)

cbind(nd_ss, pred_ss$summary)
```

Population-level predictions are often the clearest way to present a
three-factor hierarchical experiment.

## 16. Frequentist split-split crosswalk

``` r

bayes_compare_frequentist(fit_ss)
```

The crosswalk uses classical error strata for teaching. It is not used
to validate Bayesian results by p-value agreement. The two frameworks
should be reported according to their own inferential definitions.

## 17. Bayes factors in split-split models

Version 1.0.0 does not automatically enumerate a large Bayes-factor
model space for a split-split experiment. This is intentional.

If a Bayes factor is scientifically required, construct two clearly
defined `brms` models with proper priors and compare them explicitly
using bridge sampling. Priors are part of the hypotheses being compared,
and the numerical stability of the marginal-likelihood estimate must be
checked.

For exploratory treatment interpretation, posterior contrasts,
predictive checks, and predictive model comparison are usually more
transparent than an automatically generated table of many BFs.

## 18. Common split-split mistakes

Do not:

1.  use the row count as the replication count for every treatment
    factor;
2.  omit whole-plot IDs;
3.  omit subplot IDs;
4.  substitute treatment labels for experimental-unit identifiers;
5.  fit the three treatment factors in an ordinary independent-errors
    model;
6.  interpret the irrigation effect using the lowest-level residual
    alone;
7.  ignore interactions because main effects are easier to report;
8.  use an automatic post-hoc grouping without defining the scientific
    comparison and practical threshold.

## 19. Strip-plot experiments

A strip plot has a different randomization geometry. One factor is
assigned to strips in one orientation, and another factor is assigned to
perpendicular strips. Their intersections carry the factor combinations.

``` text
Block
  |
  +-- row-oriented strip factor A
  |
  +-- column-oriented strip factor B
       |
       +-- A x B intersections
```

The row and column strip experimental units are different physical or
randomization units. Their IDs must be explicit.

## 20. Strip-plot teaching data

``` r

dst <- bayes_teaching_data("strip_plot")
head(dst)
#>   block nitrogen irrigation row_strip_id column_strip_id    yield
#> 1    B1       N0        Low        B1_N0          B1_Low 5.263287
#> 2    B1       N0     Medium        B1_N0       B1_Medium 6.192738
#> 3    B1       N0       High        B1_N0         B1_High 6.739954
#> 4    B1     N100        Low      B1_N100          B1_Low 5.686269
#> 5    B1     N100     Medium      B1_N100       B1_Medium 6.685998
#> 6    B1     N100       High      B1_N100         B1_High 7.712851
dim(dst)
#> [1] 36  6
with(dst, table(block, nitrogen, irrigation))
#> , , irrigation = High
#> 
#>      nitrogen
#> block N0 N100 N200
#>    B1  1    1    1
#>    B2  1    1    1
#>    B3  1    1    1
#>    B4  1    1    1
#> 
#> , , irrigation = Low
#> 
#>      nitrogen
#> block N0 N100 N200
#>    B1  1    1    1
#>    B2  1    1    1
#>    B3  1    1    1
#>    B4  1    1    1
#> 
#> , , irrigation = Medium
#> 
#>      nitrogen
#> block N0 N100 N200
#>    B1  1    1    1
#>    B2  1    1    1
#>    B3  1    1    1
#>    B4  1    1    1
```

Here nitrogen and irrigation are used as the two perpendicular strip
factors.

## 21. Construct the strip-plot design

``` r

des_st <- bayes_strip_plot(
  data = dst,
  response = "yield",
  block = "block",
  strip_row = "nitrogen",
  strip_column = "irrigation",
  strip_row_id = "row_strip_id",
  strip_column_id = "column_strip_id"
)

des_st
#> <bayes_design>
#>   Type: Strip-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   block: block
#>   strip_row: nitrogen
#>   strip_column: irrigation
#>   strip_row_id: row_strip_id
#>   strip_column_id: column_strip_id
```

## 22. Audit strip IDs

``` r

aud_st <- bayes_design_audit(des_st)
aud_st
#> <bayes_design_audit>
#>   Design: Strip-plot design
#>   Status: PASS
aud_st$checks$one_intersection_per_strip_pair
#> [1] TRUE
```

The purpose of the audit is to make pseudoreplication and ID
inconsistencies visible before posterior fitting.

## 23. Hierarchical strip-plot model

The version 1.0.0 conceptual model is:

``` text
yield ~ nitrogen * irrigation +
        (1 | block) +
        (1 | row_strip_id) +
        (1 | column_strip_id)
```

The row-strip and column-strip random effects represent distinct
randomization scales. The interaction is observed at strip
intersections.

## 24. Why strip plots are not ordinary two-factor RCBDs

In an ordinary factorial RCBD, both factors and their combinations are
usually randomized at the same experimental-unit scale. In a strip plot,
the physical assignment of strips creates different sources of variation
for the two main effects and their interaction.

Using an ordinary RCBD model discards that information even if the data
table looks rectangular and balanced.

## 25. Fit the strip plot

``` r

fit_st <- bayes_fit(
  des_st,
  engine = "brms",
  prior = pr,
  compute = ctrl
)

bayes_diagnose(fit_st)
bayes_pp_check(fit_st)
```

## 26. Variance components in strip plots

``` r

bayes_variance_components(fit_st)
```

Substantive interpretation should distinguish variation among row strips
from variation among column strips. Their posterior distributions also
help assess how much information is available for the associated main
effects.

## 27. Interaction as a scientific target

The strip intersection is often where the scientifically interesting
combination appears. For example, the benefit of irrigation may depend
on the nitrogen regime.

Use response-scale predictions to display the full interaction:

``` r

nd_st <- expand.grid(
  nitrogen = levels(dst$nitrogen),
  irrigation = levels(dst$irrigation)
)

pred_st <- bayes_predict(
  fit_st,
  newdata = nd_st,
  type = "mean",
  re_formula = NA
)

cbind(nd_st, pred_st$summary)
```

## 28. Practical significance

A posterior contrast can be highly directional but agronomically
negligible. For every important strip-factor or interaction comparison,
consider:

- posterior median difference;
- credible interval;
- probability of positive direction;
- posterior mass in a justified ROPE;
- probability of exceeding a meaningful agronomic difference.

The dedicated ROPE vignette explains why these quantities answer
different questions.

## 29. Posterior predictive checks by randomization level

A useful check does more than compare one overall density. Consider
whether the fitted model reproduces:

- block means;
- variability among whole plots or strips;
- variability among subplots;
- treatment-combination means;
- tails and extreme values;
- heterogeneity that may suggest a richer residual model.

## 30. Model comparison

For hierarchical alternatives, PSIS-LOO can compare predictive
performance. For example, a scientifically justified alternative could
compare a model with an interaction to a model without it.

``` r

# Create two explicit model objects with the intended fixed structures,
# fit both using brms, and then compare their predictive performance.
bayes_loo(fit_st)
```

Version 1.0.0 deliberately avoids automatic model dredging.

## 31. Parameter recovery for complex designs

Before applying a custom extension to real data, simulate from a known
model, fit the model, and determine whether key parameters and variance
components can be recovered. This principle is emphasized in the Stan
tutorial supplied with the project (Annis et al. 2017).

For split-split and strip plots, recovery exercises should vary:

- fixed-effect magnitude;
- whole-plot or strip variance;
- lower-level variance;
- interaction magnitude;
- number of blocks;
- balance and missing cells.

## 32. Reporting split-split plots

A report should state:

- block definition;
- whole-plot treatment and randomization;
- subplot treatment and randomization;
- sub-subplot treatment and randomization;
- number of experimental units at each scale;
- likelihood and priors;
- group-level variance parameters;
- sampler settings and diagnostics;
- posterior fixed effects and conditional comparisons;
- posterior predictive checks;
- practical-effect criteria;
- sensitivity and predictive comparison where used.

## 33. Reporting strip plots

Also state explicitly:

- which factor was assigned to each strip orientation;
- how strip IDs were constructed;
- how intersections were observed;
- the random-effects structure representing both strip scales;
- how the interaction was interpreted.

## 34. Reproducible starter: split-split plot

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("split_split")

des <- bayes_split_split_plot(
  d, "yield", "block",
  "irrigation", "cultivar", "bioinput",
  "whole_plot_id", "subplot_id"
)

bayes_design_audit(des)

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = ctrl
)

bayes_diagnose(fit)
bayes_pp_check(fit)
bayes_variance_components(fit)
bayes_summary(fit)
```

## 35. Reproducible starter: strip plot

``` r

d <- bayes_teaching_data("strip_plot")

des <- bayes_strip_plot(
  d, "yield", "block",
  "nitrogen", "irrigation",
  "row_strip_id", "column_strip_id"
)

bayes_design_audit(des)

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = ctrl
)

bayes_diagnose(fit)
bayes_pp_check(fit)
bayes_variance_components(fit)
```

## 36. Scope of version 1.0.0

The implementation establishes Gaussian split-split and strip-plot
hierarchies with explicit experimental-unit IDs. Future releases can
extend these same roles to heterogeneous residual structures,
non-Gaussian responses, spatial field effects, incomplete designs, and
more specialized covariance structures without redefining the basic
randomization grammar.

Annis, Jeffrey, Brent J. Miller, and Thomas J. Palmeri. 2017. “Bayesian
Inference with Stan: A Tutorial on Adding Custom Distributions.”
*Behavior Research Methods* 49: 863–86.
<https://doi.org/10.3758/s13428-016-0746-9>.
