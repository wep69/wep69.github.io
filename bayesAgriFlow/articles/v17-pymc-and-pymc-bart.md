# PyMC and PyMC-BART as Optional Bayesian Backends

## 1. Why PyMC is optional rather than mandatory

`bayesAgriFlow` is an R-first package. Version 1.0.0 adds PyMC only
where it provides a useful complementary capability, beginning with
Bayesian additive regression trees through PyMC-BART. Loading the R
package must never require Python, and no Python environment is created
or modified without an explicit user request.

This separation follows the package’s capability-based architecture: the
scientific design is specified first, and a backend is selected only
when it is compatible with the requested model.

## 2. Learning objectives

The reader will learn how to inspect the optional Python environment,
understand the R-to-Python boundary, create a PyMC environment
explicitly, specify BART predictors, understand the sum-of-trees model,
fit with parallel chains, obtain posterior predictions, inspect variable
inclusion, diagnose sampling, run PSIS-LOO, preserve factor encoding,
and decide when BART is inappropriate for an interpretable agronomic
mechanism.

## 3. PyMC and probabilistic programming

PyMC is a modern probabilistic programming framework for Bayesian
models, with ArviZ used for diagnostics and model assessment (Abril-Pla
et al. 2023). PyMC-BART adds Bayesian additive regression trees as a
flexible nonparametric regression component (Chipman et al. 2010;
Quiroga et al. 2022).

## 4. Inspect the optional environment

``` r

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

The function reports status only. It does not import PyMC at package
load and does not install software.

## 5. Explicit installation only

``` r

bayes_setup_pymc(install = FALSE)
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

To create the environment, the user must explicitly request
installation:

``` r

bayes_setup_pymc(
  envname = "bayesagriflow-pymc",
  install = TRUE
)
```

This installs the current compatibility floor used by version 1.0.0 for
PyMC, PyMC-BART, ArviZ, and NumPy.

## 6. Frozen BART teaching data

``` r

d <- bayes_teaching_data("bart")
head(d)
#>   plot  nitrogen     water    soil_p cultivar     yield
#> 1    1  19.39771  54.61407 12.169619        C  8.233325
#> 2    2  34.93792  59.96484 26.590332        A 11.575237
#> 3    3 135.65068  89.26653  4.037371        A 13.824966
#> 4    4 214.30709 112.98783  5.390973        A 15.647724
#> 5    5  55.16499 102.15550  9.275538        A 14.761396
#> 6    6 140.98460  61.26670  4.481847        B 11.713375
summary(d[c("nitrogen", "water", "soil_p", "yield")])
#>     nitrogen            water            soil_p           yield       
#>  Min.   :  0.2424   Min.   : 45.33   Min.   : 4.037   Min.   : 6.836  
#>  1st Qu.: 42.2673   1st Qu.: 64.60   1st Qu.: 9.091   1st Qu.:11.710  
#>  Median : 95.2534   Median : 87.98   Median :14.312   Median :13.307  
#>  Mean   :100.7636   Mean   : 86.06   Mean   :15.043   Mean   :13.228  
#>  3rd Qu.:149.0149   3rd Qu.:105.42   3rd Qu.:21.324   3rd Qu.:15.020  
#>  Max.   :219.9399   Max.   :134.19   Max.   :27.824   Max.   :18.006
```

The dataset includes continuous predictors and a cultivar factor so that
the R-side encoding path can be demonstrated.

## 7. Why predictor encoding stays in R

`bayesAgriFlow` constructs a no-intercept model matrix in R before
calling Python. This ensures that factor levels and predictor columns
are explicit and reproducible. Prediction data are checked against the
same training schema so an unseen cultivar cannot silently become an
arbitrary numeric code.

## 8. Specify a BART design

``` r

bart_des <- bayes_bart(
  d, response = "yield",
  predictors = c("nitrogen", "water", "soil_p", "cultivar"),
  trees = 50
)
bart_des
#> <bayes_design>
#>   Type: Bayesian additive regression trees
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 160
#>   fixed: nitrogen, water, soil_p, cultivar
bayes_design_audit(bart_des)
#> <bayes_design_audit>
#>   Design: Bayesian additive regression trees
#>   Status: PASS
bayes_plan(bart_des)
#> <bayes_plan>
#>   Design: Bayesian additive regression trees
#>   Audit: PASS
#>   Engine: pymc
#>   Reason: Bayesian additive regression trees are implemented through the optional PyMC/PyMC-BART backend in version 1.0.0.
#>   Goals: estimation, prediction
```

The automatic engine is `pymc`.

## 9. What BART represents

BART models the response as a sum of many regularized regression trees.
Individual trees are intentionally weak; their ensemble can represent
nonlinearities and interactions without requiring them to be specified
in advance (Chipman et al. 2010). This flexibility is useful for
prediction but changes the interpretive target compared with a named
nonlinear curve.

## 10. Tree-count control

``` r

bart100 <- bayes_bart(d, "yield", c("nitrogen", "water", "soil_p"), trees = 100)
bart100$model_options$bart_trees
#> [1] 100
```

Tree count is not selected solely by chasing the smallest in-sample
error. Examine predictive performance, posterior stability,
computational cost, and the scientific role of the model.

## 11. Tree-depth prior parameters

`alpha` and `beta` regulate the probability of deeper trees. The
defaults favor shallow trees and regularization. Changing these values
changes the prior over function complexity and should be treated as
prior sensitivity, not hidden tuning.

## 12. Leaf response

The wrapper defaults to `leaf_response = "constant"`, the stable
reference configuration. Alternative linear or mixed leaf responses are
exposed for advanced work but should be regarded as more experimental
and validated against the installed PyMC-BART version.

## 13. PyMC computation control

``` r

ctrl_py <- bayes_compute(
  backend = "pymc",
  n_cpus = 4,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 2000,
  seed = 20260824
)
ctrl_py
#> <bayes_compute>
#>   Backend: pymc
#>   CPU budget: 4
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
```

Version 1.0.0 prevents Stan-style within-chain threading settings from
being passed to PyMC. CPU budgeting instead concerns parallel chains and
the underlying BLAS budget.

## 14. Fit BART

``` r

fit_bart <- bayes_fit(
  bart_des,
  engine = "pymc",
  compute = ctrl_py
)
fit_bart
```

Python is imported only at this point.

## 15. Compound sampling

PyMC-BART uses a sampler specialized for tree structure while ordinary
continuous parameters, such as Gaussian residual scale, can use NUTS.
The backend therefore represents a compound sampling problem rather than
pretending that all parameters are sampled by the same algorithm.

## 16. Diagnose the PyMC fit

``` r

bayes_diagnose(fit_bart)
```

The adapter summarizes ArviZ diagnostics such as R-hat and effective
sample sizes and records divergences when applicable. Diagnostic
interpretation remains conditional on which variables are sampled by
which step method.

## 17. Posterior predictions

``` r

p <- bayes_predict(
  fit_bart,
  newdata = d[1:8, ],
  type = "mean",
  ndraws = 500
)
p$summary
```

## 18. Posterior predictive observations

``` r

pp <- bayes_predict(fit_bart, d[1:8, ], type = "predictive", ndraws = 500)
```

Expected-response uncertainty and future-observation uncertainty answer
different questions and should be plotted separately.

## 19. Unseen factor levels are rejected

If prediction data contain a cultivar level not present during training,
`bayesAgriFlow` stops rather than generating a new arbitrary
dummy-variable column. New-level prediction requires a model with a
scientific rule for such levels; BART factor encoding alone cannot
invent that rule.

## 20. Variable inclusion information

``` r

vi <- bayes_bart_variable_importance(fit_bart)
vi
```

Variable inclusion summarizes how often predictors participate in tree
splitting under the fitted ensemble. It should not be described as a
causal effect, a standardized effect size, or proof that a variable is
scientifically important.

## 21. Correlated predictors

When nitrogen, soil phosphorus, and water are correlated, inclusion can
be distributed among substitutable predictors. Interpret importance
jointly with experimental design and predictive sensitivity rather than
ranking variables mechanically.

## 22. Interactions are learned implicitly

BART can represent interactions without a formula such as
`nitrogen * water`. This is useful when the interaction structure is
exploratory. If the scientific hypothesis specifically concerns a named
interaction and its magnitude, a transparent parametric or
semiparametric model may be preferable.

## 23. PSIS-LOO

``` r

bayes_loo(fit_bart)
```

The Python helper stores pointwise log likelihood when possible and
delegates predictive assessment to ArviZ. Pareto diagnostics should be
inspected rather than reporting only a scalar LOO score.

## 24. Compare BART models

``` r

fit_small <- bayes_fit(
  bayes_bart(d, "yield", c("nitrogen", "water", "soil_p"), trees = 30),
  engine = "pymc", compute = ctrl_py
)
bayes_compare_models(fit_small, fit_bart)
```

Only models fitted to the same target observations should be compared.

## 25. Do not compare brms and PyMC LOO objects naively

Version 1.0.0 requires models in
[`bayes_compare_models()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compare_models.md)
to come from the same engine. This conservative rule avoids silently
combining backend-specific summary structures. Cross-engine comparisons
can be added later only after their pointwise predictive quantities are
standardized and validated.

## 26. BART versus an agronomic dose-response curve

If the goal is to estimate an interpretable saturation rate, ED50,
asymptote, or biological optimum, a named nonlinear curve may be more
useful. If the primary goal is prediction over several interacting
predictors with unknown shape, BART may be attractive. The package
should help the user make that scientific distinction before fitting.

## 27. BART versus Gaussian process

Both are flexible. A GP encodes smoothness through a covariance kernel
in continuous predictor space; BART represents the function through a
sum of trees and naturally accommodates threshold-like interactions and
mixed predictor types. Model choice should reflect data structure,
extrapolation expectations, interpretability, and predictive validation.

## 28. BART and Bayes factors

Version 1.0.0 does not expose a generic Bayes factor for BART. For this
model class, posterior prediction, variable inclusion, uncertainty, and
out-of-sample performance are the primary outputs. Forcing a
marginal-likelihood BF onto a flexible predictive model would conflict
with the package principle that evidence indices must match a clearly
specified hypothesis.

## 29. Python provenance

The analysis record should include the Python executable, Python
version, PyMC version, PyMC-BART version, ArviZ version, NumPy version,
random seed, chain count, and CPU budget.
[`bayes_pymc_info()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_pymc_info.md)
is intended to make this provenance visible.

## 30. Environment isolation

Use a dedicated virtual environment for reproducibility. Avoid
installing PyMC packages into an unrelated project environment merely to
satisfy the package backend. An isolated environment also makes it
easier to freeze dependency versions for a publication or teaching
laboratory.

## 31. No silent installation

This rule is deliberate: package installation and model fitting should
not modify a user’s Python environment unexpectedly. If the backend is
absent, the package reports the missing capability and stops with setup
instructions.

## 32. Validation strategy

The 1.0.0 validation protocol includes an optional PyMC gate. When PyMC
is available, the frozen BART dataset is fitted with a short smoke-test
run, posterior predictions are checked for dimension and finiteness,
diagnostics are recorded, and reproducibility metadata are saved. Longer
parameter-recovery experiments belong to developer validation rather
than `R CMD check`.

## 33. Complete BART starter

``` r

d <- bayes_teaching_data("bart")
des <- bayes_bart(
  d, "yield",
  c("nitrogen", "water", "soil_p", "cultivar"),
  trees = 50
)
ctrl <- bayes_compute(backend = "pymc", n_cpus = 4,
                      chains = 4, parallel_chains = 4)
fit <- bayes_fit(des, engine = "pymc", compute = ctrl)
bayes_diagnose(fit)
bayes_predict(fit, d[1:10, ], type = "mean")
bayes_bart_variable_importance(fit)
bayes_loo(fit)
```

## 34. Scope of version 1.0.0

The PyMC engine is intentionally validated only for BART in this
release. Future adapters may expose custom probabilistic models that are
difficult to express in `brms`, but they must still return the common
`bayes_fit`, diagnostic, prediction, provenance, and reporting
interfaces. The existence of a Python backend is not a reason to
duplicate models already handled well in the R/Stan path.

## References

PyMC workflow and diagnostics are grounded in the modern PyMC/ArviZ
ecosystem (Abril-Pla et al. 2023). BART follows the model introduced by
Chipman, George, and McCulloch (Chipman et al. 2010), with the optional
implementation supplied by PyMC-BART (Quiroga et al. 2022). \#
Reproducibility checklist

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

Abril-Pla, Oriol, Virgile Andreani, Colin Carroll, et al. 2023. “PyMC: A
Modern, and Comprehensive Probabilistic Programming Framework in
Python.” *PeerJ Computer Science* 9: e1516.
<https://doi.org/10.7717/peerj-cs.1516>.

Chipman, Hugh A., Edward I. George, and Robert E. McCulloch. 2010.
“BART: Bayesian Additive Regression Trees.” *The Annals of Applied
Statistics* 4 (1): 266–98. <https://doi.org/10.1214/09-AOAS285>.

Quiroga, Miriana, Pablo G. Garay, Juan M. Alonso, Juan Martin Loyola,
and Osvaldo A. Martin. 2022. *Bayesian Additive Regression Trees for
Probabilistic Programming*. arXiv.
<https://doi.org/10.48550/arXiv.2206.03619>.
