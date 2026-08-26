# Bayesian Splines and Gaussian Processes for Flexible Agronomic Responses

## 1. Why flexible smooth models belong in the workflow

Agronomic response functions are not always well represented by a
low-degree polynomial or a named mechanistic curve. Bayesian smooths and
Gaussian processes provide flexible alternatives while retaining
posterior uncertainty. The objective is not to maximize flexibility; it
is to represent a scientifically plausible response surface without
imposing a shape that the design cannot support.

The spline interface relies on the `brms` wrappers around `mgcv` smooth
terms, whereas Gaussian-process terms use the GP facilities implemented
by `brms` (Pedersen et al. 2019; Bürkner 2017).

## 2. Learning objectives

After this vignette, the reader should be able to distinguish splines
from Gaussian processes, choose a basis dimension, understand smoothness
regularization, select a covariance kernel, use one- and two-dimensional
response surfaces, recognize interpolation versus extrapolation, fit the
model with CmdStan, examine posterior predictions, diagnose computation,
compare flexible models predictively, and report flexibility without
pretending the fitted curve is a known mechanism.

## 3. Frozen spline teaching data

``` r

ds <- bayes_teaching_data("smooth")
head(ds)
#>   block soil_moisture   biomass
#> 1     1            10  4.862119
#> 2     1            14  5.153533
#> 3     1            18  7.488206
#> 4     1            22  9.674119
#> 5     1            26 11.743713
#> 6     1            30 11.590311
summary(ds)
#>      block     soil_moisture    biomass      
#>  Min.   :1.0   Min.   :10    Min.   : 4.143  
#>  1st Qu.:2.0   1st Qu.:18    1st Qu.: 7.028  
#>  Median :3.5   Median :26    Median : 9.141  
#>  Mean   :3.5   Mean   :26    Mean   : 8.732  
#>  3rd Qu.:5.0   3rd Qu.:34    3rd Qu.:11.075  
#>  Max.   :6.0   Max.   :42    Max.   :12.474
```

The response is simulated biomass over soil moisture. The dataset is
deliberately nonlinear and is used only for instruction.

## 4. Construct a Bayesian spline

``` r

sm <- bayes_smooth(ds, "biomass", "soil_moisture", k = 10)
sm
#> <bayes_design>
#>   Type: Bayesian smoothing-spline model
#>   Response: biomass
#>   Family: Gaussian
#>   Observations: 54
#>   quantitative: soil_moisture
bayes_design_audit(sm)
#> <bayes_design_audit>
#>   Design: Bayesian smoothing-spline model
#>   Status: PASS
bayes_plan(sm)
#> <bayes_plan>
#>   Design: Bayesian smoothing-spline model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

`k` is a basis dimension, not a request for a polynomial of degree `k`.
The effective complexity is regularized by the smoothness prior.

## 5. Basis dimension is not a tuning ritual

An excessively small basis can prevent the model from representing
genuine curvature. An excessively large basis does not automatically
overfit when the smoothing penalty is effective, but it increases
computational cost and can expose weakly identified structure. Choose a
value large enough to represent plausible scientific shape and then
check adequacy rather than searching a grid solely for the smallest
information criterion.

## 6. The default thin-plate basis

``` r

sm_tp <- bayes_smooth(ds, "biomass", "soil_moisture", basis = "tp", k = 12)
sm_tp$model_options[c("smooth_basis", "smooth_k")]
#> $smooth_basis
#> [1] "tp"
#> 
#> $smooth_k
#> [1] 12
```

The package exposes the basis explicitly so the model specification is
auditable.

## 7. Group-specific smooths

When a factor modifies the shape of a response, a factor-by smooth can
be specified.

``` r

sm_by <- bayes_smooth(
  data = my_data,
  response = "yield",
  quantitative = "nitrogen",
  by = "cultivar",
  k = 10
)
```

The design must contain enough information within each group to estimate
separate shapes. Sparse group-by-predictor combinations should be
treated as a design limitation.

## 8. Hierarchical grouping with smooths

``` r

sm_block <- bayes_smooth(
  my_data, "yield", "nitrogen",
  group = "block"
)
```

A random intercept can account for grouping while the smooth represents
the population response curve. More complex random-slope or
factor-smooth structures may require a direct `brms` formula in a future
release.

## 9. Fit a spline with CmdStan

``` r

ctrl <- bayes_compute(backend = "cmdstanr", n_cpus = 8,
                      chains = 4, parallel_chains = 4)
fit_sm <- bayes_fit(sm, engine = "brms", compute = ctrl)
```

## 10. Diagnose the spline fit

``` r

bayes_diagnose(fit_sm)
bayes_pp_check(fit_sm)
```

A flexible mean function does not eliminate the need to examine the
likelihood, residual variation, posterior computation, and predictive
behavior.

## 11. Prediction over the observed domain

``` r

grid_sm <- data.frame(soil_moisture = seq(min(ds$soil_moisture), max(ds$soil_moisture), length.out = 121))
head(grid_sm)
#>   soil_moisture
#> 1      10.00000
#> 2      10.26667
#> 3      10.53333
#> 4      10.80000
#> 5      11.06667
#> 6      11.33333
```

``` r

p_sm <- bayes_predict(fit_sm, grid_sm, type = "mean")
```

## 12. Gaussian processes: a different prior on functions

A Gaussian process specifies a covariance structure among latent
function values. Nearby predictor locations can be strongly correlated,
with the degree of similarity controlled by a kernel and length scale.
This is conceptually different from selecting spline basis functions
even though both approaches can produce smooth curves.

## 13. Frozen two-dimensional GP dataset

``` r

dg <- bayes_teaching_data("gp")
head(dg)
#>   block nitrogen water     yield
#> 1     1        0    40  4.835712
#> 2     1        0    70  7.884544
#> 3     1        0   100  8.001182
#> 4     1        0   130 10.157393
#> 5     1       50    40  9.047364
#> 6     1       50    70  9.947539
summary(dg[c("nitrogen", "water", "yield")])
#>     nitrogen       water           yield       
#>  Min.   :  0   Min.   : 40.0   Min.   : 4.836  
#>  1st Qu.: 50   1st Qu.: 62.5   1st Qu.: 9.659  
#>  Median :100   Median : 85.0   Median :11.681  
#>  Mean   :100   Mean   : 85.0   Mean   :11.805  
#>  3rd Qu.:150   3rd Qu.:107.5   3rd Qu.:14.736  
#>  Max.   :200   Max.   :130.0   Max.   :17.441
```

The surface contains nonlinear nitrogen and water effects and their
joint spatial-like geometry in predictor space.

## 14. Build a two-dimensional GP

``` r

gp2 <- bayes_gp(dg, "yield", c("nitrogen", "water"))
gp2
#> <bayes_design>
#>   Type: Gaussian-process regression model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 80
#>   fixed: nitrogen, water
bayes_design_audit(gp2)
#> <bayes_design_audit>
#>   Design: Gaussian-process regression model
#>   Status: PASS
bayes_plan(gp2)
#> <bayes_plan>
#>   Design: Gaussian-process regression model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

## 15. Kernels

Version 1.0.0 exposes `exp_quad`, `matern32`, `matern52`, and
`exponential` covariance choices. Kernel choice controls assumptions
about function smoothness. A Matérn kernel may be more plausible than
the very smooth exponentiated-quadratic kernel when agronomic response
surfaces can change more abruptly.

## 16. Compare kernel specifications scientifically

``` r

gp_eq <- bayes_gp(dg, "yield", c("nitrogen", "water"), cov = "exp_quad")
gp_m3 <- bayes_gp(dg, "yield", c("nitrogen", "water"), cov = "matern32")
gp_m5 <- bayes_gp(dg, "yield", c("nitrogen", "water"), cov = "matern52")
```

Do not interpret a kernel name as a biological mechanism. It is a prior
covariance structure over functions.

## 17. Isotropic versus non-isotropic distance

`iso = TRUE` uses a common length-scale geometry across predictor
dimensions after optional scaling. If nitrogen and water operate on very
different scales or smoothness ranges, an isotropic representation may
be restrictive. Predictor scaling and scientific units therefore matter.

## 18. Approximate Gaussian processes

``` r

gp_approx <- bayes_gp(dg, "yield", c("nitrogen", "water"), k = 20)
gp_approx$model_options$gp_k
#> [1] 20
```

The optional `k` requests the approximate GP route supported by `brms`.
Approximation error and computational gain should both be checked before
it becomes a default for large datasets.

## 19. GP computation

``` r

fit_gp <- bayes_fit(gp2, engine = "brms", compute = ctrl)
bayes_diagnose(fit_gp)
bayes_pp_check(fit_gp)
```

## 20. Construct a prediction surface

``` r

grid_gp <- expand.grid(
  nitrogen = seq(min(dg$nitrogen), max(dg$nitrogen), length.out = 25),
  water = seq(min(dg$water), max(dg$water), length.out = 25)
)
head(grid_gp)
#>    nitrogen water
#> 1  0.000000    40
#> 2  8.333333    40
#> 3 16.666667    40
#> 4 25.000000    40
#> 5 33.333333    40
#> 6 41.666667    40
```

``` r

p_gp <- bayes_predict(fit_gp, grid_gp, type = "mean")
```

## 21. Do not hide unsupported regions

A rectangular prediction grid can contain nitrogen-water combinations
poorly represented by the experimental design. Plot the original
observations together with the surface and identify sparse regions.
Posterior uncertainty can be large there for a good reason.

## 22. Extrapolation is especially consequential for GPs

Outside the observed predictor cloud, the Gaussian process increasingly
relies on its prior mean and covariance assumptions. A smooth extension
beyond the data is not empirical confirmation of the extrapolated trend.

## 23. Smooth versus GP

A useful comparison asks: Is the scientific question one-dimensional and
primarily about a smooth trend, or is it about a flexible response
surface over multiple continuous predictors? Splines are often simpler
to explain; GPs provide a direct covariance-prior view over functions
and can be effective for irregular multidimensional structure.

## 24. Predictive model comparison

``` r

bayes_loo(fit_sm)
bayes_loo(fit_gp)
```

Only compare models fitted to the same observations and response target.
PSIS-LOO estimates out-of-sample predictive performance; it does not by
itself establish which mathematical representation is scientifically
true.

## 25. Derivatives and turning points

Flexible curves can be differentiated numerically draw by draw to
estimate where slopes change sign or where marginal response falls below
a meaningful threshold. Version 1.0.0 keeps the common
[`bayes_optimum()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_optimum.md)
interface for one-dimensional smooth/GP models, but any optimum must
remain inside the supported domain and be interpreted with boundary
probabilities.

## 26. Practical significance

Posterior differences between two predictor values can be compared with
a meaningful agronomic threshold rather than only asking whether a
difference is positive. This connects flexible modeling to the ROPE and
practical-significance framework developed in the dedicated
treatment-comparison vignette.

## 27. Sensitivity dimensions

For splines, vary plausible basis dimensions and examine predictive
stability. For GPs, consider plausible covariance kernels, predictor
scaling, and approximation settings. In both cases, inspect whether the
scientific conclusion is stable rather than merely whether a tuning
metric changes.

## 28. Frequentist comparison

The same `mgcv` basis can be fitted in a frequentist GAM, but Bayesian
smooth uncertainty in `brms` is part of a posterior model with explicit
priors. The comparison is useful pedagogically; it is not a reason to
convert posterior probabilities into p-values.

## 29. Complete spline starter

``` r

ds <- bayes_teaching_data("smooth")
des <- bayes_smooth(ds, "biomass", "soil_moisture", k = 12)
fit <- bayes_fit(des, engine = "brms", compute = ctrl)
bayes_diagnose(fit)
bayes_pp_check(fit)
grid <- data.frame(soil_moisture = seq(min(ds$soil_moisture), max(ds$soil_moisture), length.out = 101))
bayes_predict(fit, grid, type = "mean")
```

## 30. Complete GP starter

``` r

dg <- bayes_teaching_data("gp")
des <- bayes_gp(dg, "yield", c("nitrogen", "water"), cov = "matern32")
fit <- bayes_fit(des, engine = "brms", compute = ctrl)
bayes_diagnose(fit)
bayes_pp_check(fit)
```

## 31. Reporting flexible functions

Report the smooth basis or GP kernel, basis/approximation size,
predictor scaling, grouping structure, priors on smooth/GP scale
parameters, observed predictor domain, sampler diagnostics, posterior
predictive checks, uncertainty bands, and the exact rule for any derived
optimum. The structure of the fitted function is part of the model and
should not be hidden behind a single plot.

## 32. Scope of version 1.0.0

The current wrappers intentionally emphasize transparent
continuous-response flexible models. Future work can extend smooths and
GPs to non-Gaussian families, spatial field trials,
genotype-by-environment surfaces, and distributional regression while
preserving the same design, computation, prediction, and diagnostic
interfaces.

## References

The spline implementation builds on generalized additive modeling
principles described by Pedersen et al. (Pedersen et al. 2019), while
posterior fitting and GP terms use the `brms`/Stan framework (Bürkner
2017). \# Reproducibility checklist

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

Bürkner, Paul-Christian. 2017. “Brms: An r Package for Bayesian
Multilevel Models Using Stan.” *Journal of Statistical Software* 80 (1):
1–28. <https://doi.org/10.18637/jss.v080.i01>.

Pedersen, Eric J., David L. Miller, Gavin L. Simpson, and Noam Ross.
2019. “Hierarchical Generalized Additive Models in Ecology: An
Introduction with Mgcv.” *PeerJ* 7: e6876.
<https://doi.org/10.7717/peerj.6876>.
