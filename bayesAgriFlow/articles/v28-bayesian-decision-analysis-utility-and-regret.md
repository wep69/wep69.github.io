# Bayesian Decision Analysis: Utility and Regret

## From posterior distributions to actions

A posterior distribution answers questions about uncertain quantities. A
decision additionally requires actions and consequences. Version 1.0.0
therefore introduces an explicit utility layer rather than declaring
that the treatment with the largest posterior mean is automatically
optimal. Decision theory makes the preferences, costs, and information
assumptions visible.

The value of information literature emphasizes that learning is valuable
only through its potential to improve later decisions (Frazier and
Powell 2010). This vignette begins with the simpler current-decision
problem before considering future information in v29.

### Learning objectives

- construct action-specific utility from posterior outcome draws;
- distinguish expected utility from probability of being optimal;
- compute expected regret and identify the Bayes action;
- incorporate treatment costs and nonlinear risk preferences;
- perform sensitivity analysis when prices or utility assumptions are
  uncertain.

### The four pieces of a decision problem

- **State uncertainty:** represented by posterior draws for outcomes or
  parameters.
- **Actions:** the finite set of choices actually available to the
  decision maker.
- **Consequences:** outcomes produced when an action meets an uncertain
  state.
- **Utility or loss:** the decision maker’s valuation of those
  consequences.

### Agronomic motivating example

A fertilizer experiment can estimate posterior yield distributions for
Control, N80, N140, and N200. Choosing a dose requires more than yield.
Fertilizer cost, crop value, risk, environmental penalties, labor
constraints, or a minimum yield target may alter the preferred action.

The teaching dataset `decision` contains simulated yield and
treatment-cost information. It is an instructional dataset, not evidence
for fertilizer recommendations.

``` r

library(bayesAgriFlow)
dat <- bayes_teaching_data("decision")
aggregate(cbind(yield, treatment_cost) ~ treatment, dat, mean)
#>   treatment    yield treatment_cost
#> 1   Control 4.689498              0
#> 2      N140 6.105087            510
#> 3      N200 6.463530            760
#> 4       N80 5.760525            320
```

### Utility on posterior draws

[`bayes_utility()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_utility.md)
accepts a draws-by-actions matrix. Each row represents a posterior state
of uncertainty, and each column represents an available action. The
default utility is linear net value: outcome times value per unit minus
action cost.

Because the utility transformation is draw-wise, uncertainty in the
outcome remains visible after economic conversion.

``` r

set.seed(20260824)
y <- cbind(
  Control = rnorm(4000, 5.0, .40),
  N80     = rnorm(4000, 5.8, .45),
  N140    = rnorm(4000, 6.2, .55),
  N200    = rnorm(4000, 6.3, .75)
)
u <- bayes_utility(
  y,
  value_per_unit = 1200,
  action_costs = c(Control = 0, N80 = 350, N140 = 650, N200 = 950)
)
u
#> bayesAgriFlow posterior utility draws
#>    action expected_utility
#> 1 Control         5987.559
#> 2     N80         6607.341
#> 3    N140         6770.919
#> 4    N200         6586.427
```

### Expected utility and the Bayes action

Under a stated utility function, the Bayes action maximizes posterior
expected utility. This is a decision rule, not a hypothesis test. It
depends on the action set and utility function as well as the posterior
distribution.

[`bayes_decision()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_decision.md)
also calculates how often each action is optimal over posterior draws
and the expected regret of each action.

``` r

d <- bayes_decision(u)
d
#> bayesAgriFlow Bayesian decision
#> Recommended action: N140
#>   action expected_utility probability_optimal expected_regret
#>     N140         6770.919             0.39200        494.7216
#>      N80         6607.341             0.25200        658.2992
#>     N200         6586.427             0.32425        679.2134
#>  Control         5987.559             0.03175       1278.0816
d$table
#>          action expected_utility probability_optimal expected_regret
#> N140       N140         6770.919             0.39200        494.7216
#> N80         N80         6607.341             0.25200        658.2992
#> N200       N200         6586.427             0.32425        679.2134
#> Control Control         5987.559             0.03175       1278.0816
```

### Expected utility is not probability optimal

An action can have the largest expected utility even if another action
is optimal in more posterior draws. Expected utility accounts for the
magnitude of gains and losses, whereas probability optimal records only
which action wins in each draw.

For this reason, probability optimal is useful descriptive information
but is not the decision rule unless the utility structure specifically
justifies that criterion.

### Expected regret

For each posterior draw, regret is the utility lost by choosing an
action instead of the action that would have been optimal if that
uncertain state were known. Expected regret averages that loss over
current uncertainty.

The Bayes action that maximizes expected utility also minimizes expected
regret when both are computed from the same utility matrix.

``` r

bayes_expected_regret(d)
#>    action expected_regret
#> 3    N140        494.7216
#> 2     N80        658.2992
#> 4    N200        679.2134
#> 1 Control       1278.0816
```

### Why the highest posterior mean yield may not win

``` r

mean_yield <- colMeans(y)
mean_utility <- colMeans(u$draws)
rbind(mean_yield = mean_yield, mean_utility = mean_utility)
#>                  Control         N80        N140        N200
#> mean_yield      4.989632    5.797784    6.184099    6.280356
#> mean_utility 5987.558765 6607.341094 6770.918736 6586.426981
```

### Custom nonlinear utility

Linear monetary utility assumes constant marginal value of net return. A
risk-averse decision maker can use a concave utility transformation,
provided it is scientifically and economically justified. `utility_fun`
receives outcome, action, value per unit, and action cost.

Custom utility should be documented as carefully as the statistical
model because it can change the recommended action.

``` r

u_risk <- bayes_utility(
  y,
  value_per_unit = 1200,
  action_costs = c(0,350,650,950),
  utility_fun = function(outcome, action, value_per_unit, cost) {
    net <- value_per_unit * outcome - cost
    sqrt(pmax(net, 0))
  }
)
bayes_decision(u_risk)
#> bayesAgriFlow Bayesian decision
#> Recommended action: N140
#>   action expected_utility probability_optimal expected_regret
#>     N140         82.18563             0.39200        2.995956
#>      N80         81.21723             0.25200        3.964355
#>     N200         80.96457             0.32425        4.217012
#>  Control         77.31925             0.03175        7.862337
```

### Loss functions

A loss formulation is mathematically equivalent to a utility formulation
after a sign change, but the interpretation can be more natural for
disease, environmental damage, or threshold violations. Version 1.0.0
exposes utility directly; custom functions can return negative losses
when a loss-centered representation is desired.

A future package version can add named domain-specific loss templates
without changing the current generic contract.

### Treatment decisions directly from a fitted model

For compatible fitted treatment models,
[`bayes_treatment_decision()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_treatment_decision.md)
obtains posterior population-level treatment draws through the unified
contrast infrastructure, converts them to utility, chooses the Bayes
action, and calculates EVPI. This convenience wrapper does not bypass
model diagnostics.

``` r

# Dependency-light example with the native teaching engine.
fit <- bayes_fit(
  bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
  draws = 3000,
  seed = 20260824
)
td <- bayes_treatment_decision(
  fit,
  factor = "treatment",
  value_per_unit = 1000,
  costs = c(0, 120, 240, 360)
)
td
#> bayesAgriFlow agronomic treatment decision
#> Factor: treatment
#> Recommended action: BioC
#>   action expected_utility probability_optimal expected_regret
#>     BioC         6713.041         0.804666667        23.60863
#>     BioB         6549.008         0.191666667       187.64195
#>     BioA         6260.048         0.003666667       476.60192
#>  Control         5688.207         0.000000000      1048.44267
#> EVPI: 23.609
```

### Decision sensitivity to output value

Commodity price can change faster than the biological response.
[`bayes_decision_sensitivity()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_decision_sensitivity.md)
recalculates treatment decisions across a grid of output values while
keeping the fitted posterior fixed. This cleanly separates biological
uncertainty from economic scenario uncertainty.

``` r

sens <- bayes_decision_sensitivity(
  fit,
  factor = "treatment",
  value_grid = c(500, 750, 1000, 1250, 1500),
  costs = c(0,120,240,360)
)
sens
#>   value_per_unit recommended_action expected_utility probability_optimal
#> 1            500               BioC         3236.521           0.5536667
#> 2            750               BioC         4974.781           0.7333333
#> 3           1000               BioC         6713.041           0.8046667
#> 4           1250               BioC         8451.302           0.8360000
#> 5           1500               BioC        10189.562           0.8536667
#>   expected_regret     evpi
#> 1        31.76360 31.76360
#> 2        24.86925 24.86925
#> 3        23.60863 23.60863
#> 4        24.04959 24.04959
#> 5        25.12874 25.12874
```

### Uncertain costs and prices

Version 1.0.0 treats `value_per_unit` and action costs as supplied
values in the standard wrapper. If price or cost uncertainty is itself a
scientific target, construct the utility matrix manually using joint
posterior or scenario draws so that dependence among yield, price, and
cost is represented.

Do not pretend a deterministic price sensitivity table is a probability
distribution over future prices unless such a distribution has actually
been specified.

### Practical thresholds and utility

ROPE and meaningful-effect thresholds describe scientific relevance on
an effect scale. Utility describes consequences of actions. A yield
difference can be outside a ROPE yet economically irrelevant after
costs, or inside a biological ROPE yet operationally important under a
nonlinear penalty.

These quantities should complement rather than replace one another.

### Multi-criteria consequences

Agronomic decisions can involve yield, quality, disease, nitrogen loss,
water consumption, and risk. A multivariate posterior can feed a custom
utility function, but weights or trade-offs among outcomes must come
from the decision problem rather than from arbitrary statistical
scaling.

If stakeholders disagree on trade-offs, report several utility scenarios
instead of hiding disagreement in a single composite score.

### Actions not represented by treatments

The generic
[`bayes_utility()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_utility.md)
and
[`bayes_decision()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_decision.md)
functions are not restricted to treatment means. Columns can represent
management policies, planting dates, irrigation rules, or model-derived
optimal doses, provided each column contains posterior consequences
under the same uncertain states.

This backend-independent contract is the foundation for later sequential
and adaptive-design extensions.

### Risk and tail behavior

- Inspect utility distributions, not only means, when downside risk
  matters.
- Use posterior predictive rather than expected-response draws if
  realized outcome variation is part of the decision consequence.
- Heavy-tailed likelihoods can materially change downside utility even
  when posterior means are similar.
- If catastrophic loss is important, encode it in utility or report
  tail-risk summaries alongside expected utility.

### Decision versus treatment ranking

Posterior treatment rank probabilities answer which treatment tends to
have the highest modeled response. Decision analysis asks which
available action maximizes expected utility after costs and preferences.
The two rankings need not agree.

The package deliberately provides both because teaching one as a
substitute for the other would obscure the role of values in decisions.

### Common failure modes

- Choosing the largest posterior mean and calling it a Bayesian decision
  without stating utility.
- Using probability optimal as the sole decision rule without
  considering magnitude of consequences.
- Ignoring treatment costs or measurement costs that differ across
  actions.
- Reporting a recommendation from a model with unresolved divergences or
  poor predictive fit.
- Applying monetary utility to simulated teaching data as if it were an
  agronomic recommendation.

### Reporting checklist

- Describe the decision maker and available action set.
- Define the consequence and whether draws represent latent means or
  realized future outcomes.
- State the utility or loss function, units, prices, costs, and risk
  assumptions.
- Report expected utility, probability optimal, and expected regret
  without conflating them.
- Report sensitivity to economically plausible alternatives.
- Keep posterior model uncertainty in the consequence distribution when
  it is material.

### Reproducible starter workflow

``` r

# 1. Fit and diagnose the scientific model.
# 2. Generate posterior outcomes for every feasible action on a common draw scale.
# 3. Declare value_per_unit, action costs, and any nonlinear utility terms.
# 4. u <- bayes_utility(outcomes, value_per_unit, costs)
# 5. d <- bayes_decision(u)
# 6. bayes_expected_regret(d)
# 7. Perform utility/price/cost sensitivity.
# 8. If information collection is possible, continue to bayes_value_of_information().
```

### Connection to value of information

Expected regret shows how much is lost because the action must be
selected under current uncertainty. Value-of-information analysis asks
how much of that loss could be removed by additional information before
acting. Frazier and Powell (2010) emphasize this link between learning
and decision improvement.

The next vignette distinguishes perfect information from sample
information and explains the nested-simulation input required for EVSI.

### Study questions

1.  Which inferential quantity in this vignette answers the scientific
    question, and which quantities are only diagnostics or computational
    aids?
2.  Which assumptions enter through the likelihood, which through the
    prior, and which through the decision model?
3.  What would change if the action costs changed while the posterior
    distribution stayed fixed?
4.  What would change if the candidate model set changed while the
    observed data stayed fixed?
5.  Which result can be interpreted as a probability and which is only a
    relative predictive weight?
6.  How should sensitivity to prior choice or utility choice be
    documented in a scientific report?
7.  Which analysis step should be pre-specified when the analysis is
    intended to be confirmatory?
8.  What posterior predictive check would be most informative for the
    agronomic response considered here?
9.  Which uncertainty source is represented by posterior draws and which
    additional uncertainty source is introduced by model averaging?
10. When would a simpler model be scientifically preferable even if its
    predictive score is slightly worse?

### Worked interpretation patterns

#### Highest yield is not selected

Explain how treatment cost or nonlinear utility changes the consequence
ranking.

#### Highest probability optimal differs from Bayes action

Prefer the expected-utility rule under the declared utility and report
both quantities for transparency.

#### Expected regrets are nearly equal

Treat the decision as practically insensitive among those actions and
consider operational simplicity or additional criteria.

#### One action has rare catastrophic loss

Inspect tail utility and consider a risk-sensitive utility if it matches
stakeholder preferences.

#### Recommendation changes with crop price

Report the switching region rather than one recommendation detached from
its price assumption.

#### Recommendation changes with utility shape

Present decision sensitivity because risk preferences are scientifically
relevant assumptions.

#### Model diagnostics fail

Do not issue a final automated recommendation; resolve the statistical
model first.

#### ROPE says treatments are similar but utility differs

Explain that scientific equivalence of an effect and economic
consequences answer different questions.

#### Utility units are arbitrary

State the transformation and avoid attaching monetary interpretation
that was never defined.

#### Multiple stakeholder utilities disagree

Report scenario-specific decisions instead of averaging stakeholder
preferences without justification.

### Glossary for this vignette

#### Action

A feasible choice available before the uncertain outcome is known.

#### State

Uncertain quantity or collection of quantities affecting consequences.

#### Consequence

Outcome resulting from an action under an uncertain state.

#### Utility

Numerical representation of the value assigned to a consequence for
decision purposes.

#### Loss

Penalty representation equivalent to negative utility up to affine
transformations appropriate to the decision.

#### Bayes action

Action maximizing posterior expected utility under the stated model and
utility.

#### Probability optimal

Posterior frequency with which an action has the largest utility across
draws.

#### Regret

Utility difference between the action chosen and the best action for a
realized posterior state.

#### Expected regret

Posterior average regret for an action.

#### Decision sensitivity

Change in the recommended action across plausible utility, cost, or
price assumptions.

### Questions to answer before publication

#### Who is the decision maker?

Identify whose preferences and constraints the utility function
represents.

#### What actions are feasible?

List the choices before reporting a recommended action.

#### What outcome draws were used?

Distinguish latent means from realized posterior predictive outcomes.

#### How were costs defined?

Specify units, timing, and whether fixed/common costs were omitted
because they cancel.

#### Was risk preference modeled?

Justify linear utility or any nonlinear alternative.

#### How uncertain is the recommendation?

Report expected utility, probability optimal, and expected regret rather
than only the winning action.

#### How sensitive is it?

Vary important price, cost, and utility assumptions over defensible
ranges.

#### Does model uncertainty matter?

If so, propagate model-averaged consequences instead of conditioning on
an arbitrary single model.

Frazier, Peter I., and Warren B. Powell. 2010. “Paradoxes in Learning
and the Marginal Value of Information.” *Decision Analysis* 7 (4):
378–403. <https://doi.org/10.1287/deca.1100.0190>.
