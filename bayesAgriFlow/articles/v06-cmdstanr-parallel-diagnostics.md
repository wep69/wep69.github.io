# CmdStanR, Parallel Computing, Reproducibility, and MCMC Diagnostics

## 1. Purpose

Bayesian computation is part of the scientific method when posterior
inference depends on numerical simulation.

A report that says only “the model was fitted in Stan” omits information
needed to evaluate and reproduce the analysis.

This vignette explains the computation layer of `bayesAgriFlow` 1.0.0:

- `cmdstanr` as the recommended Stan backend;
- `rstan` as an optional alternative;
- explicit CPU budgets;
- between-chain parallelization;
- optional within-chain threading;
- seeds and strict reproducibility mode;
- warmup and posterior sampling;
- R-hat;
- bulk and tail effective sample sizes;
- Monte Carlo standard error;
- divergent transitions;
- maximum tree depth;
- E-BFMI.

## 2. Why separate chains from CPUs

Three quantities are different.

### Chains

Independent Markov chains initialized separately.

``` text
chains = 4
```

### Parallel chains

How many chains run at the same time.

``` text
parallel_chains = 4
```

### Threads per chain

How many CPU threads one chain can use in model sections compiled for
within-chain parallelism.

``` text
threads_per_chain = 2
```

The maximum requested workers are

``` math
N_{\mathrm{workers}}
=
N_{\mathrm{parallel\ chains}}
\times
N_{\mathrm{threads/chain}}.
```

[`bayes_compute()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_compute.md)
checks that this does not exceed `n_cpus`.

## 3. Conservative default

``` r

bayes_compute(
  n_cpus = 4,
  chains = 4
)
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 4
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
```

The package prioritizes between-chain parallelization. For a small
Gaussian model, this is usually easier to reason about than within-chain
threading.

## 4. Explicit eight-CPU configuration

``` r

cmp8 <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 2
)

cmp8
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 2
#>   Maximum requested workers: 8
#>   Reproducibility: standard
```

The requested worker count is eight.

The function refuses oversubscription:

``` r

bayes_compute(
  n_cpus = 4,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 2
)
#> Error:
#> ! CPU budget exceeded: parallel_chains * threads_per_chain = 8 but n_cpus = 4.
```

This is deliberate. Automatic oversubscription can make a model slower
and can make performance comparisons difficult to reproduce.

## 5. `n_cpus = "auto"`

``` r

bayes_compute(
  n_cpus = "auto",
  chains = 4
)
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 4
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
```

Version 1.0.0 uses a conservative automatic budget no larger than the
chain count. It does not silently allocate every logical thread reported
by the operating system.

For explicit benchmarking, set `n_cpus` yourself.

## 6. Why within-chain threading is optional

Within-chain parallelism requires Stan code that can split suitable
log-likelihood calculations among threads. In `brms`, this route is
based on Stan’s `reduce_sum`.

It is not guaranteed to accelerate every model. Overhead can dominate
for small Gaussian datasets. Current `brms` documentation therefore
treats this feature as experimental and recommends it for users who
understand the model’s computational structure.

Consequently:

``` r

threads_per_chain = 1
```

is the general default.

## 7. Strict reproducibility mode

``` r

cmp_strict <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 2,
  reproducibility = "strict",
  seed = 20260824
)

cmp_strict
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 2
#>   Maximum requested workers: 8
#>   Reproducibility: strict
```

When threading is active, strict mode requests static scheduling through
`brms::threading(static = TRUE)`.

Static scheduling plus a fixed seed is useful when exact reproducibility
of a threaded computation is required. Dynamic scheduling can change the
order in which partial sums are evaluated.

Exact numerical identity is a stronger goal than scientific
reproducibility. For many validation tasks, agreement within Monte Carlo
error is the relevant criterion, but the package allows the stricter
mode when needed.

## 8. Inspect backend availability

``` r

info <- bayes_compute_info(
  bayes_compute(n_cpus = 4, chains = 4)
)

info
#> $compute
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 4
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
#> 
#> $brms
#> $brms$installed
#> [1] TRUE
#> 
#> $brms$version
#> [1] "2.23.0"
#> 
#> 
#> $cmdstanr
#> $cmdstanr$installed
#> [1] TRUE
#> 
#> $cmdstanr$version
#> [1] "0.9.0"
#> 
#> 
#> $rstan
#> $rstan$installed
#> [1] TRUE
#> 
#> $rstan$version
#> [1] "2.32.7"
#> 
#> 
#> $BayesFactor
#> $BayesFactor$installed
#> [1] TRUE
#> 
#> $BayesFactor$version
#> [1] "0.9.12.4.8"
#> 
#> 
#> $posterior
#> $posterior$installed
#> [1] TRUE
#> 
#> $posterior$version
#> [1] "1.7.0"
#> 
#> 
#> $reticulate
#> $reticulate$installed
#> [1] TRUE
#> 
#> $reticulate$version
#> [1] "1.46.0"
#> 
#> 
#> $pymc
#> $pymc$available
#> [1] FALSE
#> 
#> $pymc$reticulate
#> [1] TRUE
#> 
#> $pymc$python
#> [1] "H:/uv/AppDataLocalUv/cache/archive-v0/MfuOKTFtveE-Nd3l_RFiM/Scripts/python.exe"
#> 
#> $pymc$python_version
#> [1] "3.12.10 (tags/v3.12.10:0cc8128, Apr  8 2025, 12:21:36) [MSC v.1943 64 bit (AMD64)]"
#> 
#> $pymc$pymc
#> [1] FALSE
#> 
#> $pymc$pymc_version
#> [1] NA
#> 
#> $pymc$pymc_bart
#> [1] FALSE
#> 
#> $pymc$pymc_bart_version
#> [1] NA
#> 
#> $pymc$arviz
#> [1] FALSE
#> 
#> $pymc$arviz_version
#> [1] NA
#> 
#> $pymc$numpy
#> [1] TRUE
#> 
#> $pymc$numpy_version
#> [1] "2.5.2"
#> 
#> 
#> $cmdstan_version
#> [1] "2.37.0"
#> 
#> $cmdstan_path
#> [1] "C:/Users/wep69/.cmdstan/cmdstan-2.37.0"
```

The returned structure records whether optional packages are installed
and, when possible, the configured CmdStan version and path.

The package does not install CmdStan silently.

## 9. Local CmdStan preparation

On a workstation with R installed:

``` r

install.packages("cmdstanr",
  repos = c("https://stan-dev.r-universe.dev", getOption("repos"))
)

cmdstanr::check_cmdstan_toolchain()
cmdstanr::install_cmdstan(cores = 4)
cmdstanr::cmdstan_version()
```

Installation commands belong to the local environment setup. A CRAN
package must not download and compile CmdStan during package loading.

## 10. Configure a split-plot analysis

``` r

d <- bayes_teaching_data("split_plot")

des <- bayes_split_plot(
  d,
  "yield",
  "block",
  "irrigation",
  "cultivar",
  "whole_plot_id"
)

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 4,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 1000,
  seed = 20260824,
  adapt_delta = 0.95,
  max_treedepth = 12
)
```

## 11. Warmup and sampling

HMC/NUTS adapts quantities such as step size and the mass matrix during
warmup. Warmup samples are not treated as posterior draws.

If four chains use 1000 post-warmup iterations each, the nominal
posterior sample contains 4000 draws before considering autocorrelation
and effective sample size.

Nominal draws and effective draws are not the same quantity.

## 12. Fit locally

The following is intentionally not evaluated when the vignette is built.

``` r

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior(),
  compute = cmp
)
```

`brms` compiles the requested model to Stan, while `cmdstanr` provides
the CmdStan execution interface.

The use of Stan for multilevel Bayesian models is described in Bürkner
(2017), and the role of HMC/NUTS for complex posterior geometry is
introduced pedagogically by Annis et al. (2017).

## 13. R-hat

R-hat compares within-chain and between-chain behavior. Values
materially above one indicate that chains have not mixed sufficiently
into the same stationary distribution.

``` r

diag <- bayes_diagnose(fit)
head(diag$summary)
```

Version 1.0.0 flags R-hat values above 1.01 by default.

A threshold is a screening rule, not a guarantee. Always inspect the
broader diagnostic pattern.

## 14. Effective sample size

MCMC draws are generally autocorrelated. The effective sample size
estimates how much independent-sample information the chain contains.

Two forms are especially useful:

- bulk ESS for central posterior summaries;
- tail ESS for tail probabilities and interval limits.

A long chain with poor mixing can have a much smaller ESS than its
nominal number of draws.

Version 1.0.0 issues a warning when supported ESS values fall below its
default screening limit.

## 15. Monte Carlo standard error

MCSE quantifies uncertainty caused by finite posterior simulation.

Suppose two model fits produce posterior medians that differ by less
than their Monte Carlo precision. That difference should not be
interpreted as a scientific discrepancy between backends.

This is particularly important in cross-backend validation.

## 16. Divergent transitions

A divergent transition indicates that HMC encountered posterior geometry
that its numerical integration did not explore reliably.

Divergences after warmup can bias posterior summaries.

Possible responses include:

- reparameterizing the model;
- using more informative priors;
- checking scaling;
- examining hierarchical funnels;
- increasing `adapt_delta` when appropriate.

Increasing `adapt_delta` is not a universal cure. A persistent geometry
problem may require a better model parameterization.

[`bayes_diagnose()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_diagnose.md)
marks a Stan fit with divergences as `FAIL` in version 0.1.0.

## 17. Maximum tree depth

NUTS builds a trajectory adaptively. Reaching the maximum tree depth
means that the sampler wanted to continue building a longer trajectory.

Tree-depth saturation is not identical to a divergence. It often
indicates inefficient exploration.

The package records the number of iterations at the configured
`max_treedepth`.

## 18. E-BFMI

The energy Bayesian fraction of missing information examines how
effectively the sampler explores the energy distribution.

When sampler energy is available,
[`bayes_diagnose()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_diagnose.md)
calculates a minimum chain-level E-BFMI summary. Version 1.0.0 uses 0.3
as a warning screening value.

As with every diagnostic threshold, the number is a trigger for
investigation, not a substitute for model understanding.

## 19. Diagnostic status

[`bayes_diagnose()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_diagnose.md)
returns:

``` text
PASS
WARNING
FAIL
```

The status is a workflow gate.

- `FAIL` indicates a major issue such as divergences or an R-hat
  screening failure.
- `WARNING` indicates concerns such as low ESS, tree-depth saturation,
  or low E-BFMI.
- `PASS` means the implemented screening rules did not flag the fit.

`PASS` does not prove that the statistical model is scientifically
adequate. Posterior predictive checking is still required.

## 20. Posterior predictive checking is different

Sampler diagnostics ask:

> Did the algorithm explore the posterior?

Posterior predictive checking asks:

> Can the fitted model reproduce important features of the observed
> data?

``` r

ppc <- bayes_pp_check(fit, ndraws = 500)
ppc$observed
summary(ppc$replicated$mean)
```

A model can have perfect MCMC diagnostics and still fit the data poorly.

## 21. Prior predictive checking is different again

Prior predictive checking asks:

> Before conditioning on these data, what observations can the prior
> model generate?

This check can reveal implausibly broad or narrow priors even when
posterior sampling later converges.

BARG treats prior predictive checking, posterior predictive checking,
and computational diagnostics as separate reporting components (Kruschke
2021).

## 22. Performance benchmarking

A meaningful benchmark should freeze:

- data;
- model formula;
- priors;
- CmdStan and compiler versions;
- chain count;
- warmup and sampling iterations;
- seed;
- CPU budget;
- thread configuration.

Then compare configurations such as:

``` text
4 chains x 1 thread
4 chains x 2 threads
2 chains x 4 threads
```

while avoiding CPU oversubscription.

Do not benchmark one configuration after changing the model or prior.

## 23. One CPU versus N CPUs validation

The local validation workflow should compare:

``` r

cmp1 <- bayes_compute(
  n_cpus = 1,
  chains = 4,
  parallel_chains = 1
)

cmp4 <- bayes_compute(
  n_cpus = 4,
  chains = 4,
  parallel_chains = 4
)
```

The scientific expectation is not necessarily bit-identical draws.
Validate:

- posterior estimates within MCSE;
- compatible credible intervals;
- no new diagnostic failures;
- same scientific interpretation.

For strict threaded reproducibility, use strict mode and verify the
local backend behavior.

## 24. Heavy-vignette policy

A full split-plot Stan fit should not be recomputed during every package
installation or `R CMD check`.

The release workflow is:

1.  retain the complete fitting code in the vignette;
2.  mark expensive chunks as not evaluated in the distributed source;
3.  execute them in a developer validation script;
4.  store the actual result object;
5.  store seed, versions, CPU settings, and data hash;
6.  build figures/tables from that stored object;
7.  include the precomputed vignette output in the official source
    build;
8.  run `R CMD check --as-cran` on the exact final tarball.

This prevents documentation convenience from being confused with
numerical validation.

## 25. Complete local computation script

``` r

library(bayesAgriFlow)

des <- bayes_split_plot(
  bayes_teaching_data("split_plot"),
  response = "yield",
  block = "block",
  whole_plot = "irrigation",
  subplot = "cultivar",
  whole_plot_id = "whole_plot_id"
)

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1500,
  iter_sampling = 2000,
  seed = 20260824,
  adapt_delta = 0.97,
  max_treedepth = 12
)

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior(),
  compute = cmp
)

diag <- bayes_diagnose(fit)
print(diag)

if (diag$status == "FAIL") {
  stop("Resolve sampling diagnostics before interpretation.")
}

bayes_contrasts(fit, "irrigation")
bayes_contrasts(fit, "cultivar")
bayes_pp_check(fit, ndraws = 500)
```

## 26. What to record in a scientific report

At minimum:

- R version;
- `bayesAgriFlow` version;
- `brms` version;
- `cmdstanr` version;
- CmdStan version;
- operating system;
- compiler/toolchain when relevant;
- model formula;
- priors;
- chains;
- warmup iterations;
- sampling iterations;
- seed;
- CPU budget;
- parallel chains;
- threads per chain;
- `adapt_delta`;
- `max_treedepth`;
- R-hat range/max;
- minimum bulk and tail ESS;
- divergences;
- tree-depth hits;
- E-BFMI if available.

The goal is not to fill a report with software trivia. These details
allow another researcher to distinguish a scientific difference from a
computational difference.

## References

Annis, Jeffrey, Brent J. Miller, and Thomas J. Palmeri. 2017. “Bayesian
Inference with Stan: A Tutorial on Adding Custom Distributions.”
*Behavior Research Methods* 49: 863–86.
<https://doi.org/10.3758/s13428-016-0746-9>.

Bürkner, Paul-Christian. 2017. “Brms: An r Package for Bayesian
Multilevel Models Using Stan.” *Journal of Statistical Software* 80 (1):
1–28. <https://doi.org/10.18637/jss.v080.i01>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.
