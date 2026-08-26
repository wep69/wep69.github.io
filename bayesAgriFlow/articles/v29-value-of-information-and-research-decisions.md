# Value of Information and Research Decisions

## What is additional information worth?

Additional sampling is valuable only if it can change a decision in a
way that improves expected utility. Value-of-information analysis
therefore combines posterior uncertainty with the action set and utility
function. It is not a generic measure of parameter uncertainty.

Expected value of perfect information provides an upper bound on the
value of eliminating the modeled uncertainty. Expected value of sample
information quantifies the expected gain from a specific future study or
sampling plan. The EVSI literature makes clear that the latter is a
pre-posterior calculation and generally requires nested or approximated
integration (Ades et al. 2004).

### Learning objectives

- compute EVPI from posterior utility draws;
- interpret EVPI as conditional on actions, utility, and the current
  posterior;
- understand the nested-simulation contract used by
  [`bayes_evsi()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_evsi.md);
- distinguish EVSI from expected parameter information or posterior
  variance reduction;
- connect information value to whether additional agronomic
  experimentation is economically worthwhile.

### Current decision first

Before valuing information, define the current decision. Let
$`U(a,  heta)`$ denote utility for action $`a`$ under uncertain state \$
heta\$. Current expected utility for action $`a`$ averages
$`U(a,   heta)`$ over the current posterior. The current Bayes action
maximizes that expectation.

Perfect information changes the order of operations: for each posterior
state, one can first identify the best action and then average its
utility.

### Expected value of perfect information

EVPI is the expected utility obtained if the uncertain state were known
before acting minus the expected utility of the best action under
current information. It is non-negative apart from Monte Carlo error and
is an upper bound on the expected benefit of any real information source
under the same decision model.

It does not tell the analyst what experiment to run. It tells whether
eliminating current modeled uncertainty could have decision value.

``` r

library(bayesAgriFlow)
set.seed(20260824)
u <- cbind(
  Control = rnorm(5000, 5000, 300),
  N80 = rnorm(5000, 5250, 350),
  N140 = rnorm(5000, 5320, 500),
  N200 = rnorm(5000, 5280, 650)
)
bayes_value_of_information(u)
#>   current_expected_utility expected_utility_with_perfect_information     evpi
#> 1                 5303.463                                  5725.904 422.4405
#>   information_cost net_evpi
#> 1                0 422.4405
bayes_value_of_information(u, information_cost = 50)
#>   current_expected_utility expected_utility_with_perfect_information     evpi
#> 1                 5303.463                                  5725.904 422.4405
#>   information_cost net_evpi
#> 1               50 372.4405
```

### EVPI is decision-specific

- Changing treatment costs can change EVPI even if the posterior yield
  model does not change.
- Removing an action can reduce the value of information because fewer
  choices can benefit from learning.
- Changing risk preferences changes utility and can change the value of
  resolving tail uncertainty.
- Adding irrelevant parameter uncertainty does not increase EVPI unless
  that uncertainty changes consequences across actions.

### The opportunity loss interpretation

EVPI can also be viewed as the expected regret of the current Bayes
action when uncertainty could be perfectly resolved before acting. This
makes its relation to the previous vignette direct.

When posterior distributions for all actions are nearly ordered with
little overlap, EVPI can be small even if parameter intervals are wide.
Conversely, modest parameter uncertainty can have high information value
when decisions are finely balanced and consequences are large.

### Sample information

A real experiment rarely reveals the uncertain state perfectly. It
produces noisy data. EVSI averages the benefit of making the decision
after seeing a future dataset over the predictive distribution of all
datasets that might be observed.

This requires a pre-posterior calculation: simulate or integrate over
future data, update the model for each future dataset, compute
conditional expected utility for every action, choose the best action
under that updated information, and then average.

### The explicit bayes_evsi input contract

[`bayes_evsi()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_evsi.md)
does not accept raw posterior parameter draws and call them EVSI. The
argument `conditional_expected_utility` must have one row per simulated
future dataset and one column per action. Each cell is the expected
utility of that action after updating the analysis with that future
dataset.

This strict contract is intentional. It prevents a common conceptual
error in which current posterior variability is mistaken for
pre-posterior sample information.

### Teaching EVSI dataset

The `voi` teaching dataset contains simulated future-dataset identifiers
and conditional expected utilities for four fertilizer actions. It is a
frozen instructional object for learning the EVSI calculation, not the
output of a validated agronomic trial.

``` r

voi <- bayes_teaching_data("voi")
head(voi)
#>   future_dataset weather_state eu_Control   eu_N80  eu_N140  eu_N200
#> 1              1           dry   4654.475 5125.554 5669.735 5096.831
#> 2              2           dry   4711.459 5379.796 5585.114 5445.550
#> 3              3           wet   4731.919 5475.513 5878.698 5957.379
#> 4              4           wet   4763.949 5586.926 5882.979 5833.302
#> 5              5           dry   4575.576 5233.719 5358.298 5207.191
#> 6              6           wet   4766.010 5756.371 5988.613 5475.192
ceu <- as.matrix(voi[c("eu_Control", "eu_N80", "eu_N140", "eu_N200")])
colMeans(ceu)
#> eu_Control     eu_N80    eu_N140    eu_N200 
#>   4704.647   5454.724   5736.280   5470.338
```

### Compute a teaching EVSI

``` r

set.seed(20260824)
current_u <- cbind(
  Control = rnorm(4000, 5000, 350),
  N80 = rnorm(4000, 5180, 380),
  N140 = rnorm(4000, 5260, 500),
  N200 = rnorm(4000, 5230, 620)
)
colnames(ceu) <- colnames(current_u)
evsi <- bayes_evsi(current_u, ceu, information_cost = 0)
evsi
#>   current_expected_utility expected_utility_with_sample_information     evsi
#> 1                 5245.544                                 5749.167 503.6227
#>   evpi_upper_bound information_cost net_evsi evsi_exceeds_evpi_flag
#> 1         418.1179                0 503.6227                   TRUE
```

### Why EVSI should not exceed EVPI

Under a mathematically coherent shared decision model, a finite noisy
sample cannot be more valuable than perfect information, so EVSI is
bounded above by EVPI. In Monte Carlo workflows, estimated EVSI can
exceed an estimated EVPI because of simulation error, inconsistent
utility inputs, or biased approximations.

[`bayes_evsi()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_evsi.md)
reports an `exceeds_evpi` diagnostic flag instead of silently truncating
the result. A violation should trigger review of simulation size, nested
estimation, common random numbers, model consistency, and Monte Carlo
error.

### Nested simulation algorithm

- 1.  Draw a current posterior state or generate a future dataset from
      the posterior predictive distribution.
- 2.  Fit or update the Bayesian model using that simulated future
      dataset.
- 3.  Compute posterior expected utility of every action conditional on
      the simulated dataset.
- 4.  Retain the maximum conditional expected utility for that dataset.
- 5.  Repeat across many future datasets.
- 6.  Average the maxima and subtract the current maximum expected
      utility.

### Why nested MCMC is expensive

Naive EVSI can require one posterior update for every simulated future
dataset. With hierarchical Stan models, this can be computationally
prohibitive. Published EVSI methods therefore include regression,
moment-matching, Gaussian approximations, importance sampling, and other
strategies tailored to specific decision models (Ades et al. 2004).

Version 1.0.0 deliberately provides a transparent generic aggregation
interface rather than claiming to implement every specialized EVSI
approximation.

### Research cost and net EVSI

The gross EVSI is the expected improvement in decision utility before
the cost of acquiring the sample. A study is economically attractive
only if the expected benefit exceeds relevant research cost under the
declared utility accounting.

`information_cost` is subtracted to provide a net value, but analysts
must decide which fixed and variable research costs belong in that
quantity.

``` r

bayes_evsi(current_u, ceu, information_cost = 75)
#>   current_expected_utility expected_utility_with_sample_information     evsi
#> 1                 5245.544                                 5749.167 503.6227
#>   evpi_upper_bound information_cost net_evsi evsi_exceeds_evpi_flag
#> 1         418.1179               75 428.6227                   TRUE
```

### Sample size as a decision variable

EVSI generally changes with sample size because larger studies produce
more informative future posteriors, but research cost also grows. An
optimal study size can be framed as the size that maximizes expected net
benefit of sampling, not simply the size that reaches a conventional
power threshold.

Version 1.0.0 prepares the API for this extension but does not silently
optimize sample size because a valid implementation requires a model for
future data, analysis, action timing, and sampling cost.

### Value of information versus Bayes-factor design analysis

Bayes-factor design analysis asks about evidence behavior under a future
sampling plan. EVSI asks about expected decision benefit after observing
future data. A study can be informative in an evidential sense yet have
low decision value if the preferred action is unlikely to change.

The two planning perspectives can be used together when both scientific
evidence and operational decision quality matter.

### Partial perfect information

Expected value of partial perfect information asks what would be gained
by resolving only a subset of uncertain quantities. This can identify
which measurement domains deserve additional research. Version 1.0.0
reserves this as a future extension rather than estimating it with an
undocumented shortcut.

The same utility-draw contract can support EVPPI once the conditioning
algorithm is implemented and validated.

### Agronomic examples

- Whether another fertilizer dose-response trial is worth conducting
  before recommending a rate.
- Whether additional soil sampling is valuable before variable-rate
  fertilization.
- Whether another environment should be added to a genotype trial before
  cultivar deployment.
- Whether a more expensive disease diagnostic is worth obtaining before
  treatment.
- Whether additional irrigation monitoring is worth its cost before
  selecting a water-management policy.

### Temporal and spatial information

When future data are spatially or temporally dependent, the sampling
design changes both the amount and pattern of information. EVSI
simulation should reproduce that dependence rather than assuming
independent measurements.

The spatial and repeated-measures model objects introduced in versions
0.3.0 and 0.5.0 provide a statistical foundation for later
design-specific value-of-information modules.

### Model uncertainty inside VOI

If model uncertainty materially affects action consequences,
information-value calculations should propagate it. One route is to
simulate consequences from a model-averaged predictive distribution.
Another is to embed the candidate-model identity in the pre-posterior
simulation.

Do not calculate EVPI from one selected model and then claim that it
represents all recognized model uncertainty.

### Monte Carlo diagnostics for VOI

- Repeat the outer simulation with different seeds.
- Increase the number of future datasets until EVSI stabilizes relative
  to the decision scale.
- Assess uncertainty in nested conditional expected utilities.
- Use common random numbers when appropriate to reduce variance in
  comparing study designs.
- Investigate any estimated EVSI greater than EVPI.
- Report numerical uncertainty when it is material to the study/no-study
  decision.

### Decision consistency checks

- All utility matrices must use the same action labels and units.
- The current utility and future conditional utilities must correspond
  to the same utility function.
- Information cost must not be double-counted in action-specific costs.
- The future-data generator must match the proposed sampling design.
- The updating analysis used in simulated future datasets should match
  the intended future analysis.

### Common failure modes

- Calling posterior variance reduction EVSI without connecting it to
  actions and utility.
- Using current posterior draws as rows of conditional expected utility.
- Ignoring research cost when interpreting positive gross EVSI.
- Optimizing sample size using an unvalidated approximate future-data
  model.
- Treating an EVPI computed under teaching prices as a real economic
  recommendation.

### Reporting checklist

- Define the current decision and action set.
- Report the current posterior utility model.
- Report EVPI and the utility units.
- For EVSI, describe the proposed sampling design and predictive data
  generator.
- Describe how each simulated future dataset was analyzed or
  approximated.
- Report outer and inner simulation sizes and numerical sensitivity.
- Report research cost assumptions and net information value.
- Discuss whether model, price, and structural uncertainty were
  propagated.

### Reproducible EVSI pseudocode

``` r

# current_fit <- ...
# current_outcomes <- posterior consequences under each action
# current_u <- bayes_utility(current_outcomes, value_per_unit, action_costs)
#
# for (s in seq_len(S)) {
#   future_data <- simulate_future_study(current_fit, design = proposed_design)
#   updated_fit <- refit_or_update(future_data)
#   conditional_expected_utility[s, ] <- expected_utility_by_action(updated_fit)
# }
#
# bayes_evsi(
#   current_utility = current_u,
#   conditional_expected_utility = conditional_expected_utility,
#   information_cost = research_cost
# )
```

### Current scope and future extensions

Version 1.0.0 supplies EVPI exactly from utility draws up to Monte Carlo
error and provides a generic EVSI aggregator with an explicit
pre-posterior input contract. It does not yet automate nested refitting,
EVPPI, optimized study allocation, or adaptive sequential design.

Those extensions are reserved because their validity depends strongly on
design-specific data generation and computation. The current interface
is intentionally strict enough that such methods can be added without
changing the meaning of the existing functions.

### References

Ades et al. (2004) develops expected value of sample information
calculations in decision modeling and illustrates the computational
challenge of pre-posterior integration. Frazier and Powell (2010)
provides a broader decision-analytic perspective on the marginal value
of information.

The package uses these ideas to teach a general principle: uncertainty
has information value only through its possible effect on decisions.

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

#### EVPI is near zero

Additional information cannot improve the modeled decision much under
the present action set and utility, even if some parameters remain
uncertain.

#### EVPI is large

There is meaningful opportunity loss from current uncertainty, but EVPI
alone does not identify which experiment can recover that value.

#### EVSI is much smaller than EVPI

The proposed study resolves only a fraction of the decision-relevant
uncertainty.

#### Net EVSI is negative

The proposed information is not worthwhile under the stated research
cost and utility assumptions.

#### EVSI exceeds EVPI numerically

Treat this as a diagnostic failure or Monte Carlo approximation issue,
not as a scientifically meaningful result.

#### EVSI changes strongly with sample size

Compare net value after sampling cost rather than choosing the largest
gross EVSI.

#### Two study designs have similar EVSI

Consider logistics, ethical burden, spatial coverage, or other
operational constraints.

#### Parameter variance falls but EVSI remains low

Learning occurs but does not alter the optimal decision enough to create
much utility gain.

#### Model averaging raises EVPI

Structural model uncertainty contributes to current opportunity loss and
should remain in the information calculation.

#### Study value depends on price scenario

Report scenario-specific VOI instead of one universal value.

### Glossary for this vignette

#### Current information

Posterior knowledge before the proposed additional study.

#### Perfect information

Hypothetical knowledge sufficient to select the best action for each
uncertain state before acting.

#### EVPI

Expected increase in maximum achievable utility under perfect
information relative to current information.

#### Sample information

Noisy information produced by a specified future sampling or
experimental design.

#### EVSI

Expected improvement in decision utility after observing and analyzing
the proposed sample.

#### Pre-posterior analysis

Expectation taken over future datasets before they are observed, with
posterior updating nested inside.

#### Conditional expected utility

Expected action utility after conditioning on one simulated future
dataset.

#### Net EVSI

Gross EVSI minus the cost assigned to obtaining the information.

#### EVPPI

Value of perfectly resolving only a subset of uncertain quantities;
reserved for future package implementation.

#### Research design utility

Decision-theoretic objective used to compare alternative future
information-gathering plans.

### Questions to answer before publication

#### What decision can the study change?

If no action depends on the information, the VOI framing is incomplete.

#### How were future data generated?

Describe the predictive model and sampling design used in pre-posterior
simulation.

#### How was the model updated?

State whether each future dataset was refitted exactly or analyzed with
an approximation.

#### What is the Monte Carlo error?

Show stability across simulation size and seeds when it affects the
study decision.

#### Which costs are counted?

Separate information-acquisition cost from action costs already included
in utility.

#### Is EVSI below EVPI?

Check the theoretical ordering and investigate violations.

#### What uncertainty is omitted?

Discuss price, structural, spatial, temporal, or model uncertainties not
propagated.

#### What future module is needed?

If EVPPI, adaptive design, or automated nested updating is required,
state that version 1.0.0 provides the contract but not that specialized
algorithm.

Ades, A. E., G. Lu, and K. Claxton. 2004. “Expected Value of Sample
Information Calculations in Medical Decision Modeling.” *Medical
Decision Making* 24 (2): 207–27.
<https://doi.org/10.1177/0272989X04263162>.

Frazier, Peter I., and Warren B. Powell. 2010. “Paradoxes in Learning
and the Marginal Value of Information.” *Decision Analysis* 7 (4):
378–403. <https://doi.org/10.1287/deca.1100.0190>.
