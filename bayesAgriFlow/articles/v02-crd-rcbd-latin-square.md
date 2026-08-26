# CRD, RCBD, and Latin Square: One Bayesian Design-Aware Workflow

## 1. Why these three designs belong in one vignette

Completely randomized, randomized complete block, and Latin-square
experiments are often taught as three separate ANOVA procedures. That
organization can hide their common logic.

All three ask about treatment differences. What changes is the
**randomization structure and the nuisance variation that the model must
represent**.

This vignette therefore uses one progression:

``` math
\text{CRD}
\rightarrow
\text{add one blocking direction}
\rightarrow
\text{add two blocking directions}.
```

The Bayesian workflow stays recognizable while the design object
changes.

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  identify experimental units and treatment replication in a CRD;
2.  understand why an RCBD is not a CRD with an extra descriptive
    column;
3.  verify the defining row-column treatment arrangement of a Latin
    square;
4.  audit each design before fitting;
5.  fit the same Gaussian posterior-estimation engine to all three
    designs;
6.  construct treatment contrasts on the response scale;
7.  define ROPE and meaningful thresholds in scientific units;
8.  compare posterior uncertainty across designs;
9.  understand how `BayesFactor` and `brms` provide alternative
    capability routes;
10. use the frequentist ANOVA only as a conceptual crosswalk.

## 3. CRD: treatment assignment at the experimental-unit level

Load the simulated bioinput example.

``` r

crd_dat <- bayes_teaching_data("crd")
head(crd_dat)
#>   treatment replicate    yield
#> 1   Control         1 6.388969
#> 2   Control         2 6.146232
#> 3   Control         3 5.854985
#> 4   Control         4 6.708967
#> 5   Control         5 5.931464
#> 6   Control         6 5.665910
with(crd_dat, table(treatment))
#> treatment
#>    BioA    BioB    BioC Control 
#>       8       8       8       8
```

The experimental unit is the unit independently assigned to a treatment.
The `replicate` identifier is descriptive and is not a blocking factor.

``` r

crd <- bayes_crd(
  crd_dat,
  response = "yield",
  treatment = "treatment"
)

crd_audit <- bayes_design_audit(crd)
crd_audit
#> <bayes_design_audit>
#>   Design: Completely randomized design
#>   Status: PASS
```

A balanced example is convenient for teaching, but balance is not a
substitute for checking whether observations actually correspond to
independent experimental units.

## 4. CRD model

The native version 1.0.0 model is

``` math
y_{ij}
=
\mu+\tau_i+\varepsilon_{ij},
\qquad
\varepsilon_{ij}\sim N(0,\sigma^2),
```

expressed in the treatment-coded model matrix used by R.

``` r

crd_fit <- bayes_fit(
  crd,
  engine = "native",
  prior = bayes_prior("regularizing"),
  draws = 2000
)

bayes_summary(crd_fit)
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

The coefficient table depends on the chosen reference level. Treatment
comparisons are usually more directly scientific than raw
treatment-coded coefficients.

``` r

crd_con <- bayes_contrasts(
  crd_fit,
  factor = "treatment",
  rope = c(-0.20, 0.20),
  meaningful = 0.40
)

crd_con
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

For a contrast $`A-B`$, `p_a_gt_b` means

``` math
P(\mu_A-\mu_B>0\mid y).
```

The sign follows the printed contrast label.

## 5. RCBD: why blocking changes the analysis

Now consider cultivars evaluated in four blocks.

``` r

rcbd_dat <- bayes_teaching_data("rcbd")
with(rcbd_dat, table(block, cultivar))
#>      cultivar
#> block C1 C2 C3 C4 C5
#>    B1  1  1  1  1  1
#>    B2  1  1  1  1  1
#>    B3  1  1  1  1  1
#>    B4  1  1  1  1  1
```

In an RCBD, each block contains the treatment set. Blocks are introduced
to account for a systematic source of variation that is not itself the
principal treatment question.

``` r

rcbd <- bayes_rcbd(
  rcbd_dat,
  response = "yield",
  treatment = "cultivar",
  block = "block"
)

bayes_design_audit(rcbd)
#> <bayes_design_audit>
#>   Design: Randomized complete block design
#>   Status: PASS
```

The fixed-design native model is

``` math
y_{ij}
=
\mu+\tau_i+b_j+\varepsilon_{ij}.
```

For a multilevel Stan analysis, the package’s hierarchical compiler
instead uses a grouping-level block intercept:

``` math
y_{ij}
=
\mu+\tau_i+u_j+\varepsilon_{ij},
\qquad
u_j\sim N(0,\sigma_b^2).
```

Both preserve block structure, but the interpretation and prior
specification of the block contribution differ. `bayesAgriFlow` does not
pretend they are identical parameterizations.

## 6. Fit and compare cultivars

``` r

rcbd_fit <- bayes_fit(
  rcbd,
  engine = "native",
  prior = bayes_prior("regularizing"),
  draws = 2000
)

rcbd_con <- bayes_contrasts(
  rcbd_fit,
  factor = "cultivar",
  meaningful = 0.30
)

rcbd_con
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

A meaningful threshold of 0.30 is only an example for the simulated
teaching scale. A real agronomic analysis should justify its threshold
from biological, economic, management, or measurement considerations.

## 7. Why block effects are not treatment replicates

Suppose four blocks are present. That does not mean that every
individual observation is independent evidence for the block effect in
the same sense as a treatment replicate. Blocking is part of the
randomization structure.

This distinction becomes more important in split plots, where whole-plot
and subplot treatments are randomized at different experimental-unit
levels. The same design-first logic used here is carried into
`v05-split-plot-experiments`.

## 8. Latin square: two nuisance directions

The Latin-square teaching data contain five rows, five columns, and five
treatments.

``` r

lat_dat <- bayes_teaching_data("latin_square")
with(lat_dat, table(row, treatment))
#>     treatment
#> row  T1 T2 T3 T4 T5
#>   R1  1  1  1  1  1
#>   R2  1  1  1  1  1
#>   R3  1  1  1  1  1
#>   R4  1  1  1  1  1
#>   R5  1  1  1  1  1
with(lat_dat, table(column, treatment))
#>       treatment
#> column T1 T2 T3 T4 T5
#>     C1  1  1  1  1  1
#>     C2  1  1  1  1  1
#>     C3  1  1  1  1  1
#>     C4  1  1  1  1  1
#>     C5  1  1  1  1  1
```

A valid Latin square requires each treatment exactly once in every row
and every column.

``` r

latin <- bayes_latin_square(
  lat_dat,
  response = "biomass",
  treatment = "treatment",
  row = "row",
  column = "column"
)

lat_audit <- bayes_design_audit(latin)
lat_audit
#> <bayes_design_audit>
#>   Design: Latin square design
#>   Status: PASS
lat_audit$checks$latin_square_order
#>       rows    columns treatments 
#>          5          5          5
```

The model is

``` math
y_{ijk}
=
\mu+\tau_i+\rho_j+\kappa_k+\varepsilon_{ijk}.
```

The row and column effects absorb two orthogonal nuisance directions in
the classical balanced design.

## 9. Fit the Latin-square model

``` r

lat_fit <- bayes_fit(
  latin,
  engine = "native",
  draws = 2000
)

lat_con <- bayes_contrasts(
  lat_fit,
  factor = "treatment",
  rope = c(-0.15, 0.15)
)

lat_con
#>    contrast level_a level_b        mean      median        sd      lower
#> 1   T1 - T2      T1      T2  0.08208001  0.07961668 0.1541862 -0.2166039
#> 2   T1 - T3      T1      T3 -0.41184903 -0.41075186 0.1548080 -0.7127316
#> 3   T1 - T4      T1      T4 -0.52544011 -0.52494939 0.1530565 -0.8405044
#> 4   T1 - T5      T1      T5 -0.86957984 -0.86656127 0.1509645 -1.1839085
#> 5   T2 - T3      T2      T3 -0.49392904 -0.48939501 0.1563814 -0.8239100
#> 6   T2 - T4      T2      T4 -0.60752012 -0.60342953 0.1596438 -0.9297986
#> 7   T2 - T5      T2      T5 -0.95165985 -0.94959369 0.1577965 -1.2594387
#> 8   T3 - T4      T3      T4 -0.11359108 -0.11545993 0.1608531 -0.4260919
#> 9   T3 - T5      T3      T5 -0.45773081 -0.45954665 0.1540831 -0.7510123
#> 10  T4 - T5      T4      T5 -0.34413973 -0.34389053 0.1574842 -0.6527597
#>          upper p_a_gt_b p_a_lt_b     pd rope_pct p_abs_gt_meaningful
#> 1   0.38864029   0.7035   0.2965 0.7035   0.6030                  NA
#> 2  -0.10176066   0.0050   0.9950 0.9950   0.0470                  NA
#> 3  -0.22254172   0.0000   1.0000 1.0000   0.0060                  NA
#> 4  -0.58199450   0.0000   1.0000 1.0000   0.0000                  NA
#> 5  -0.19656844   0.0010   0.9990 0.9990   0.0150                  NA
#> 6  -0.28621681   0.0000   1.0000 1.0000   0.0015                  NA
#> 7  -0.64358140   0.0000   1.0000 1.0000   0.0000                  NA
#> 8   0.20746185   0.2365   0.7635 0.7635   0.5310                  NA
#> 9  -0.15452743   0.0025   0.9975 0.9975   0.0215                  NA
#> 10 -0.02673594   0.0170   0.9830 0.9830   0.1070                  NA
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

The posterior treatment contrast is still interpreted on the response
scale. What changed is the model’s adjustment for row and column
structure.

## 10. A common API makes design differences visible

The three analyses use the same downstream functions:

``` r

bayes_diagnose(crd_fit)
#> <bayes_diagnostics>
#>   Engine: native
#>   Status: PASS
#>   The native engine samples directly from the conjugate posterior. R-hat, ESS, divergences, tree depth, and E-BFMI are not MCMC diagnostics for this engine.
bayes_diagnose(rcbd_fit)
#> <bayes_diagnostics>
#>   Engine: native
#>   Status: PASS
#>   The native engine samples directly from the conjugate posterior. R-hat, ESS, divergences, tree depth, and E-BFMI are not MCMC diagnostics for this engine.
bayes_diagnose(lat_fit)
#> <bayes_diagnostics>
#>   Engine: native
#>   Status: PASS
#>   The native engine samples directly from the conjugate posterior. R-hat, ESS, divergences, tree depth, and E-BFMI are not MCMC diagnostics for this engine.

bayes_pp_check(crd_fit, ndraws = 100)$observed
#>         mean        sd minimum  maximum      q10      q90
#> 10% 6.484965 0.5355904 5.66591 7.676907 5.855358 7.127354
bayes_pp_check(rcbd_fit, ndraws = 100)$observed
#>         mean        sd  minimum  maximum      q10      q90
#> 10% 7.521104 0.4674796 6.506797 8.494594 7.004709 7.992937
bayes_pp_check(lat_fit, ndraws = 100)$observed
#>         mean        sd  minimum  maximum      q10    q90
#> 10% 5.052597 0.4960192 4.321289 6.012633 4.395908 5.7355
```

The common API is useful only because the design object remains
explicit. A unified interface should not erase the differences among
randomization structures.

## 11. Frequentist crosswalk

``` r

bayes_compare_frequentist(crd_fit)$anova
#> Analysis of Variance Table
#> 
#> Response: yield
#>           Df Sum Sq Mean Sq F value    Pr(>F)    
#> treatment  3 4.1604 1.38681  8.2057 0.0004488 ***
#> Residuals 28 4.7321 0.16901                      
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
bayes_compare_frequentist(rcbd_fit)$anova
#> Analysis of Variance Table
#> 
#> Response: yield
#>           Df Sum Sq Mean Sq F value   Pr(>F)   
#> cultivar   4 2.3774 0.59435  5.4817 0.009551 **
#> block      3 0.4737 0.15790  1.4563 0.275643   
#> Residuals 12 1.3011 0.10842                    
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
bayes_compare_frequentist(lat_fit)$anova
#> Analysis of Variance Table
#> 
#> Response: biomass
#>           Df Sum Sq Mean Sq F value    Pr(>F)    
#> treatment  4 3.5003 0.87506 11.5329 0.0004458 ***
#> row        4 0.8979 0.22448  2.9585 0.0648180 .  
#> column     4 0.5962 0.14905  1.9643 0.1643315    
#> Residuals 12 0.9105 0.07588                      
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

These are familiar ANOVA decompositions. They are shown to help students
connect designs across inferential frameworks.

The Bayesian posterior does not obtain its validity from agreement with
a frequentist p-value. Likewise, a discrepancy between a p-value and a
BF does not imply that one software result is wrong. The procedures
condition on different inferential structures (Doorn et al. 2021;
Tendeiro et al. 2025).

## 12. Optional Bayes-factor evidence

``` r

if (requireNamespace("BayesFactor", quietly = TRUE)) {
  bf_crd <- bayes_fit(crd, engine = "bayesfactor")
  bf_rcbd <- bayes_fit(rcbd, engine = "bayesfactor")
  bf_lat <- bayes_fit(latin, engine = "bayesfactor")

  bayes_evidence(bf_crd)
  bayes_evidence(bf_rcbd)
  bayes_evidence(bf_lat)
}
```

The evidence adapter uses the priors and model-comparison conventions of
`BayesFactor`. Therefore, use BF for its own evidence question and
retain the posterior-estimation output for effect magnitude.

## 13. Optional Stan formulation

For an RCBD:

``` r

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 4,
  chains = 4,
  parallel_chains = 4
)

fit_rcbd_stan <- bayes_fit(
  rcbd,
  engine = "brms",
  prior = bayes_prior(),
  compute = cmp
)

bayes_diagnose(fit_rcbd_stan)
bayes_contrasts(fit_rcbd_stan, "cultivar")
```

The Stan route is especially useful when future versions add
heterogeneous variance, non-Gaussian responses, repeated measures,
spatial terms, or other hierarchical components that cannot be
represented by the narrow conjugate engine.

## 14. Design mistakes the package cannot repair

No Bayesian method can repair an experiment whose replication or
randomization was misunderstood.

Examples include:

- treating subsamples as independent treatment replicates;
- calling a column “block” even though it was not part of randomization;
- fitting a Latin-square model to a table in which treatments do not
  occur once per row and column;
- ignoring missing cells that destroy the original balance;
- assigning a block effect after data inspection merely to improve fit.

The audit catches some structural symptoms. It cannot reconstruct
undocumented randomization.

## 15. Interpretation template

For each design, report:

1.  the experimental unit;
2.  treatment assignment and blocking structure;
3.  response likelihood;
4.  prior specification and justification;
5.  posterior treatment estimates;
6.  treatment contrasts with credible intervals;
7.  direction probability only when scientifically useful;
8.  ROPE or meaningful thresholds only when justified;
9.  posterior predictive checks;
10. prior sensitivity;
11. MCMC diagnostics if a Stan route was used;
12. BF only for explicitly defined evidence questions.

## 16. Complete comparative script

``` r

# CRD
crd <- bayes_crd(
  bayes_teaching_data("crd"),
  "yield", "treatment"
)
fit_crd <- bayes_fit(crd, draws = 4000)
bayes_contrasts(fit_crd, "treatment")

# RCBD
rcbd <- bayes_rcbd(
  bayes_teaching_data("rcbd"),
  "yield", "cultivar", "block"
)
fit_rcbd <- bayes_fit(rcbd, draws = 4000)
bayes_contrasts(fit_rcbd, "cultivar")

# Latin square
latin <- bayes_latin_square(
  bayes_teaching_data("latin_square"),
  "biomass", "treatment", "row", "column"
)
fit_latin <- bayes_fit(latin, draws = 4000)
bayes_contrasts(fit_latin, "treatment")
```

## References

Doorn, Johnny van, Don van den Bergh, Udo Böhm, et al. 2021. “The JASP
Guidelines for Conducting and Reporting a Bayesian Analysis.”
*Psychonomic Bulletin & Review* 28: 813–26.
<https://doi.org/10.3758/s13423-020-01798-5>.

Tendeiro, Jorge N., Rink Hoekstra, Tsz Keung Wong, and Henk A. L. Kiers.
2025. “Introduction to the Bayes Factor: A Shiny/r App.” *Teaching
Statistics* 47 (1): 5–16. <https://doi.org/10.1111/test.12380>.
