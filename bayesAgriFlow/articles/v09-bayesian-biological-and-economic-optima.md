# Bayesian Biological and Economic Optima

## Why an optimum is a posterior quantity

Agronomic recommendations often report a single optimum obtained by
substituting point estimates into a fitted polynomial. A Bayesian
workflow instead evaluates the optimum for every posterior draw. This
produces a distribution for the optimum and makes visible whether
uncertainty is concentrated in the interior of the experimental domain
or at a boundary.

## 1. Fit the response curve

``` r

dat <- bayes_teaching_data("dose")
des <- bayes_polynomial(dat, "yield", "nitrogen", degree = 2)
fit <- bayes_fit(des, engine = "native", draws = 4000, seed = 20260824)
bayes_summary(fit)
#>       parameter          mean        median           sd         lower
#> 1   (Intercept)  5.3560991010  5.3595166826 2.485550e-01  4.8522651602
#> 2      nitrogen  0.0586069067  0.0586244585 5.845454e-03  0.0465969612
#> 3 I(nitrogen^2) -0.0001910791 -0.0001909068 2.819298e-05 -0.0002464967
#> 4         sigma  0.6007190529  0.5942416904 7.675215e-02  0.4716133503
#>          upper p_positive p_negative pd
#> 1  5.847503704          1          0  1
#> 2  0.070297446          1          0  1
#> 3 -0.000133816          0          1  1
#> 4  0.776488772          1          0  1
```

The model is only a useful basis for optimization if its curve is
scientifically plausible and adequately represents the observed response
pattern.

## 2. Define the admissible domain

The optimization domain should ordinarily be the experimentally
investigated range unless external evidence justifies extrapolation.

``` r

domain <- range(dat$nitrogen)
domain
#> [1]   0 200
```

## 3. Biological optimum

``` r

opt <- bayes_optimum(fit, domain = domain)
opt
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>           153.5           139           178        9.869002        9.57184
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>        10.15469                0          0.00225    0.99775         <NA>
```

The reported interval is a posterior interval for the maximizing input
level under the fitted model and the chosen domain. The boundary
probabilities are particularly useful. A large probability at the upper
boundary means that the experiment may not have located an interior
maximum, even if a point-estimate quadratic vertex appears nearby.

## 4. Sensitivity to grid resolution

The current implementation uses posterior prediction on a dense grid,
which works for linear, quadratic, cubic, and later nonlinear response
curves without requiring a separate analytic derivative for every model
family.

``` r

opt_fine <- bayes_optimum(fit, domain = domain, grid = 801)
opt_fine
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>           153.5           139      177.7563        9.869004        9.57184
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>        10.15469                0          0.00225    0.99775         <NA>
```

Grid sensitivity should be negligible relative to posterior uncertainty.
If not, increase resolution.

## 5. Economic optimum

The biological optimum maximizes expected response. An economic optimum
maximizes net return under explicit price and cost assumptions.

``` r

econ <- bayes_economic_optimum(
  fit,
  domain = domain,
  output_price = 1.20,
  input_cost_per_unit = 0.80,
  fixed_cost = 0
)
econ
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper net_return_median net_return_lower
#>               0             0             0           6.43142         5.822718
#>  net_return_upper p_positive_net_return factor_level
#>          7.017004                     1         <NA>
```

The price values in this teaching example are arbitrary. Real
applications must document units, market date, source, and whether price
uncertainty is ignored or modeled.

## 6. Interpreting economic uncertainty

The posterior distribution of the economic optimum incorporates
uncertainty in the fitted response curve, while
[`bayes_economic_optimum()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_economic_optimum.md)
treats supplied prices and costs as fixed constants. When prices, costs,
or utilities are uncertain, construct the corresponding posterior
utility draws and use the current decision functions, including
[`bayes_decision()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_decision.md),
[`bayes_expected_regret()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_expected_regret.md),
and
[`bayes_value_of_information()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_value_of_information.md).

## 7. Boundary optima

A posterior optimum at zero or at the maximum tested input is not a
numerical failure. It is information about the combination of posterior
curve shape and admissible domain. Report boundary probabilities instead
of silently discarding these posterior draws.

## 8. Linear models

A linear response model generally has its constrained optimum at a
boundary. This is scientifically different from estimating an interior
turning point.

``` r

lin <- bayes_fit(bayes_regression(dat, "yield", "nitrogen"), draws = 1500)
bayes_optimum(lin, domain = domain)
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>             200           200           200        10.45657       9.849217
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>         11.0211                0                1          0         <NA>
```

## 9. Relation to ROPE and practical relevance

An optimum can be statistically well identified but practically
unimportant. Conversely, a broad optimum region may contain many input
levels whose expected responses differ negligibly. For discrete
candidate actions, version 1.0.0 can evaluate expected regret directly
with
[`bayes_expected_regret()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_expected_regret.md).
Continuous plateau-aware or regret-defined optimum sets remain a
specialized extension, so users should still combine the optimum
distribution with scientifically meaningful response differences and
economic criteria.

## 10. CmdStanR workflow

For publication-grade hierarchical or complex curve models, use the Stan
backend and report computation details.

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)
fit_stan <- bayes_fit(des, engine = "brms", compute = ctrl)
bayes_diagnose(fit_stan)
bayes_pp_check(fit_stan)
bayes_optimum(fit_stan, domain = domain)
```

## 11. Reporting template

Report the response model, experimental domain, prior specification,
posterior predictive assessment, posterior optimum median and interval,
probability that the optimum lies at either domain boundary, predicted
response at the optimum, and all economic assumptions when an economic
optimum is used. Distinguish clearly between biological and economic
optima.

## 12. What version 1.0.0 does not yet claim

The current optimum module does not automatically select the best
polynomial degree, does not extrapolate beyond the supplied domain, does
not propagate stochastic commodity prices, does not fit plateau models
automatically, and does not treat the posterior optimum as a causal
recommendation outside the experimental design. These restrictions are
intentional.

## 13. The optimum is conditional on a model

The posterior optimum is not a model-free property of the raw data. It
is a derived quantity from the fitted response surface and the
admissible domain. Thus, uncertainty in the optimum has at least three
conceptually different sources:

1.  posterior uncertainty in model parameters;
2.  uncertainty about the appropriate response-function family;
3.  uncertainty about the decision inputs, such as prices and costs.

Version 1.0.0 propagates the first source through posterior draws. It
does not automatically average over response-function families or
stochastic economic inputs.

## 14. Why optimizing posterior mean coefficients is not enough

Consider a quadratic response with posterior draws

``` math
\beta_0^{(s)},\;\beta_1^{(s)},\;\beta_2^{(s)}.
```

One common shortcut is to calculate the vertex from posterior mean
coefficients. That gives one number and discards the uncertainty and
covariance among coefficients.

[`bayes_optimum()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_optimum.md)
instead evaluates the response surface for each posterior draw and
determines the best admissible input for that draw. The resulting set

``` math
x_*^{(1)},\ldots,x_*^{(S)}
```

is the posterior distribution of the constrained optimum under the
fitted model.

## 15. Why grid optimization is used

For a simple quadratic, an analytical vertex is available. For cubic,
qualitative-by-quantitative, nonlinear, and future flexible models, a
separate closed-form solution may not exist.

A grid-based posterior optimizer gives the package one consistent
interface. The user controls grid resolution and can verify that
numerical resolution is negligible relative to posterior uncertainty.

## 16. Check grid convergence

A practical sensitivity exercise is:

``` r

opt_201 <- bayes_optimum(fit, domain = domain, grid = 201)
opt_401 <- bayes_optimum(fit, domain = domain, grid = 401)
opt_801 <- bayes_optimum(fit, domain = domain, grid = 801)

rbind(
  grid201 = opt_201$summary,
  grid401 = opt_401$summary,
  grid801 = opt_801$summary
)
#>         optimum_median optimum_lower optimum_upper response_median
#> grid201          153.0           139      178.0000        9.868996
#> grid401          153.5           139      178.0000        9.869002
#> grid801          153.5           139      177.7563        9.869004
#>         response_lower response_upper p_lower_boundary p_upper_boundary
#> grid201       9.571804       10.15469                0          0.00225
#> grid401       9.571840       10.15469                0          0.00225
#> grid801       9.571840       10.15469                0          0.00225
#>         p_interior factor_level
#> grid201    0.99775         <NA>
#> grid401    0.99775         <NA>
#> grid801    0.99775         <NA>
```

If the optimum summary changes materially as the grid becomes finer,
numerical resolution is still contributing too much uncertainty.

## 17. Curvature and interior solutions

A quadratic point estimate may have a vertex even when substantial
posterior mass corresponds to nearly linear, convex, or weakly curved
responses.

The constrained posterior optimizer handles these cases by allowing the
best value to occur at a boundary. Therefore, the posterior distribution
itself reveals whether an interior optimum is consistently supported.

A report should not discard boundary draws to make the optimum interval
look more precise.

## 18. Interpreting boundary probabilities

Suppose

``` text
P(lower boundary) = 0.01
P(interior)       = 0.34
P(upper boundary) = 0.65
```

The scientific conclusion is not “the optimum equals the posterior
median.” The experiment provides substantial posterior support for
continued increase through the upper tested dose. A larger dose range
may be needed before an interior optimum can be identified.

Boundary probability is therefore a design diagnostic as well as an
optimization result.

## 19. Predicted response at the optimum

The optimizer also records the expected response associated with each
posterior optimum. This provides a posterior distribution for the best
modeled response within the domain.

``` r

summary(opt$optimum_draws)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   124.5   148.0   153.5   154.6   160.0   200.0
summary(opt$response_draws)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   9.273   9.769   9.869   9.866   9.965  10.434
```

The posterior response at the optimum should not be confused with a
posterior predictive distribution for a new individual experimental
unit. It is based on the expected response curve.

## 20. Flat response regions

A single maximizing dose can be misleading when the curve is almost flat
near its maximum. Several neighboring doses may have practically
indistinguishable expected responses.

For discrete candidate actions, the current decision layer provides
regret-based comparisons. Continuous optimum sets based on regret or
practical response loss. In version 1.0.0, the analyst can inspect
posterior response draws around the optimum and define a scientifically
negligible loss.

For example, a fertilizer recommendation may prefer the smallest dose
whose expected yield is within a practically negligible amount of the
posterior maximum.

## 21. Optimum versus minimum effective dose

These are different decision problems.

- **Optimum:** choose the input maximizing a utility or response.
- **Minimum effective dose:** choose the smallest input that achieves a
  defined target relative to a control or reference response.

The current optimizer addresses the first problem. The 1.0 decision
functions may support target attainment and constrained minimum-input
rules.

## 22. Biological optimum by cultivar

For a qualitative-by-quantitative model, the optimizer can operate for a
specified factor level.

``` r

qq <- bayes_teaching_data("qual_quant")
fit_qq <- bayes_fit(
  bayes_qual_quant(qq, "yield", "cultivar", "nitrogen"),
  draws = 3000,
  seed = 20260824
)

bayes_optimum(
  fit_qq,
  domain = range(qq$nitrogen),
  factor_level = levels(qq$cultivar)[1]
)
```

In a linear factor-by-dose model, the constrained optimum will usually
occur at a boundary. Cultivar-specific interior optima require a
response model with appropriate curvature, which is planned for a future
extension.

## 23. Economic objective function

The current economic optimizer uses

``` math
U(x) = p_y\,\mu(x) - c_x x - c_0,
```

where $`p_y`$ is output price, $`c_x`$ is input cost per unit, and
$`c_0`$ is a fixed cost.

The optimizer evaluates this objective for each posterior response
curve. Thus biological uncertainty propagates to the economically
optimal input.

## 24. Fixed costs and the argmax

A fixed cost that does not vary with dose shifts all net returns by the
same amount. Consequently, it changes the absolute net return but not
the dose that maximizes the current linear-cost objective.

It can still change the posterior probability that maximum net return is
positive.

This distinction is useful when interpreting `fixed_cost`.

## 25. Input-price sensitivity

Economic recommendations should not be reported for one arbitrary price
combination without considering sensitivity when prices are volatile.

``` r

e1 <- bayes_economic_optimum(fit, domain, 1.20, 0.60)
e2 <- bayes_economic_optimum(fit, domain, 1.20, 0.80)
e3 <- bayes_economic_optimum(fit, domain, 1.20, 1.00)

rbind(
  low_input_cost = e1$summary,
  reference_cost = e2$summary,
  high_input_cost = e3$summary
)
#>                 optimum_median optimum_lower optimum_upper net_return_median
#> low_input_cost               0             0             0           6.43142
#> reference_cost               0             0             0           6.43142
#> high_input_cost              0             0             0           6.43142
#>                 net_return_lower net_return_upper p_positive_net_return
#> low_input_cost          5.822718         7.017004                     1
#> reference_cost          5.822718         7.017004                     1
#> high_input_cost         5.822718         7.017004                     1
#>                 factor_level
#> low_input_cost          <NA>
#> reference_cost          <NA>
#> high_input_cost         <NA>
```

The three analyses condition on fixed cost assumptions. They are a
sensitivity analysis, not a probabilistic model for commodity prices.

## 26. Output-price sensitivity

Similarly, changes in commodity value alter the marginal economic
benefit of the response.

``` r

y1 <- bayes_economic_optimum(fit, domain, 0.90, 0.80)
y2 <- bayes_economic_optimum(fit, domain, 1.20, 0.80)
y3 <- bayes_economic_optimum(fit, domain, 1.50, 0.80)

rbind(
  low_output_price = y1$summary,
  reference_price = y2$summary,
  high_output_price = y3$summary
)
#>                   optimum_median optimum_lower optimum_upper net_return_median
#> low_output_price               0             0             0          4.823565
#> reference_price                0             0             0          6.431420
#> high_output_price              0             0             0          8.039275
#>                   net_return_lower net_return_upper p_positive_net_return
#> low_output_price          4.367039         5.262753                     1
#> reference_price           5.822718         7.017004                     1
#> high_output_price         7.278398         8.771256                     1
#>                   factor_level
#> low_output_price          <NA>
#> reference_price           <NA>
#> high_output_price         <NA>
```

A final recommendation should state the currency, physical units,
reference date, and source of every economic input.

## 27. Probability of positive net return

The economic output includes the posterior probability that the best
modeled net return is positive under the supplied cost and price
assumptions.

This probability is conditional on:

- the fitted biological response model;
- the admissible domain;
- fixed economic inputs;
- the definition of net return used by the function.

It is not a general probability that adopting the treatment will be
profitable in every farm or future season.

## 28. Economic optimum and uncertainty in prices

The 1.0 decision layer can represent uncertain prices through posterior
utility draws or scenario sensitivity rather than treating a fixed-price
optimum as a probability distribution. A posterior decision workflow may
draw jointly from:

``` text
biological response uncertainty
+
commodity-price uncertainty
+
input-cost uncertainty
+
other management uncertainty
```

and optimize expected utility or expected loss. That is a broader
decision problem than the current conditional economic optimum.

## 29. Expected regret

For each posterior state, one can define regret as the difference
between the utility achieved by the state-specific best decision and the
utility achieved by a candidate fixed decision.

[`bayes_expected_regret()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_expected_regret.md)
can summarize expected regret across the posterior. This is particularly
attractive when a wide range of doses have similar expected outcomes and
a single point optimum is unstable.

## 30. Relation to ROPE

ROPE and optimization answer different questions.

ROPE asks whether a parameter or contrast lies in a region considered
practically negligible. Optimization asks which decision maximizes an
objective within a domain.

They can be combined. For example, a lower fertilizer dose may be
preferred if its posterior yield loss relative to the maximum is almost
entirely within a scientifically negligible region.

## 31. Model-form sensitivity

A posterior optimum from a quadratic model should be compared with
optima from other scientifically plausible curve families when those
models are available.

For version 1.0.0, at minimum compare plausible linear, quadratic, and
cubic representations where justified and examine whether the optimum
changes substantially.

``` r

f1 <- bayes_fit(bayes_polynomial(dat, "yield", "nitrogen", 1),
                engine = "brms", compute = ctrl)
f2 <- bayes_fit(bayes_polynomial(dat, "yield", "nitrogen", 2),
                engine = "brms", compute = ctrl)
f3 <- bayes_fit(bayes_polynomial(dat, "yield", "nitrogen", 3),
                engine = "brms", compute = ctrl)

bayes_compare_models(linear = f1, quadratic = f2, cubic = f3)
```

Predictive support for one model does not remove uncertainty about all
possible functional forms. Report the candidate set considered.

## 32. Posterior predictive adequacy before optimization

Optimization magnifies model misspecification. A curve can fit average
values reasonably but produce a misleading derivative or turning point.

Before calculating an optimum, examine:

- fit near the lower and upper experimental boundaries;
- residual structure;
- treatment-specific lack of fit;
- influential extreme observations;
- unexplained heteroscedasticity;
- whether the chosen curve shape is biologically credible.

Do not optimize a model that has not first been shown to mimic the
relevant data features adequately.

## 33. Designing experiments to estimate an optimum

An experiment intended to estimate an optimum should include dose levels
on both sides of the expected optimum. If all tested doses lie on the
increasing part of the response, an interior optimum cannot be estimated
reliably by curvature alone.

Posterior boundary probability can therefore inform the design of a
subsequent experiment.

## 34. Replication and optimum precision

Increasing replication improves estimation of the response curve but
does not compensate for a poorly chosen dose range. Many replicates of
doses all below the turning point can produce a precise estimate of the
rising curve and still leave the optimum outside the experimental
domain.

Dose placement and replication are complementary design decisions.

## 35. Hierarchical dose-response optima

Future versions will combine the optimum machinery with
random-coefficient and multi-environment response curves. In such
models, several estimands become possible:

- population-average optimum;
- environment-specific optimum;
- genotype-specific optimum;
- posterior distribution of variability in optimum among environments;
- optimum under a new environment drawn from the modeled population.

These quantities must not be conflated.

## 36. Reporting a biological optimum

A strong report should contain:

1.  the response model;
2.  the tested input levels and experimental domain;
3.  likelihood and priors;
4.  posterior predictive assessment;
5.  posterior optimum median and interval;
6.  lower- and upper-boundary probabilities;
7.  posterior expected response at the optimum;
8.  sensitivity to grid resolution;
9.  sensitivity to plausible model forms;
10. a clear statement that inference is conditional on the modeled
    domain.

## 37. Reporting an economic optimum

Add:

- output price and unit;
- input cost and unit;
- fixed-cost definition;
- date and source of economic inputs;
- posterior net-return distribution;
- probability of positive net return;
- sensitivity to alternative plausible prices and costs.

If prices are treated as fixed, state that explicitly.

## 38. Common mistakes

Avoid:

1.  calculating an optimum from posterior mean coefficients only;
2.  discarding posterior boundary optima;
3.  extrapolating outside the tested range without justification;
4.  reporting an optimum without its posterior interval;
5.  optimizing a poorly fitting model;
6.  treating a flat response peak as a precisely identified point
    decision;
7.  calling the biological optimum the economic optimum;
8.  omitting units from prices and costs;
9.  assuming fixed price inputs represent economic uncertainty;
10. presenting a model-conditional optimum as a universal agronomic
    recommendation.

## 39. Complete advanced workflow

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("dose")

des <- bayes_polynomial(
  d,
  response = "yield",
  quantitative = "nitrogen",
  degree = 2
)

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

domain <- range(d$nitrogen)

bio <- bayes_optimum(
  fit,
  domain = domain,
  grid = 801
)

econ <- bayes_economic_optimum(
  fit,
  domain = domain,
  output_price = 1.20,
  input_cost_per_unit = 0.80,
  grid = 801
)

bio
econ
```

## 40. Scope and roadmap

Version 1.0.0 provides posterior constrained biological and economic
optima for supported quantitative response curves. Later versions can
add plateau and nonlinear curves, stochastic economic inputs, posterior
regret, utility functions, value of information, multi-environment
decision rules, and model averaging or projection-based selection.

The central rule will remain unchanged: uncertainty in the model and in
the decision inputs must propagate into the recommendation rather than
disappear behind a single point estimate.
