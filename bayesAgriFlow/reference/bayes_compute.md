# Configure Bayesian computation

Separates CPU budget, chain parallelism, and optional within-chain
threading. \`parallel_chains \* threads_per_chain\` cannot exceed
\`n_cpus\`.

## Usage

``` r
bayes_compute(backend = c("cmdstanr", "rstan", "pymc"),
                          n_cpus = "auto", chains = 4,
                          parallel_chains = NULL, threads_per_chain = 1,
                          iter_warmup = 1000, iter_sampling = 1000,
                          seed = 20260824, adapt_delta = 0.95,
                          max_treedepth = 12,
                          reproducibility = c("standard", "strict"))
```

## Arguments

- backend:

  \`"cmdstanr"\`, \`"rstan"\`, or \`"pymc"\`.

- n_cpus:

  Positive integer or \`"auto"\`.

- chains:

  Number of Markov chains.

- parallel_chains:

  Maximum chains run simultaneously.

- threads_per_chain:

  Threads per chain. Values above one are intended only for models that
  support Stan threading.

- iter_warmup:

  Warmup iterations.

- iter_sampling:

  Sampling iterations per chain.

- seed:

  Random seed.

- adapt_delta:

  Stan target acceptance probability.

- max_treedepth:

  Stan maximum tree depth.

- reproducibility:

  \`"standard"\` or \`"strict"\`. Strict mode requests static threading
  when within-chain threading is used.

## Value

A \`bayes_compute\` object.

## Examples

``` r
# Example 1: four chains on four CPUs
bayes_compute(n_cpus = 4, chains = 4)
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 4
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
# Example 2: eight-CPU budget with four parallel chains
bayes_compute(n_cpus = 8, chains = 4, parallel_chains = 4)
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
# Example 3: explicit within-chain threading
bayes_compute(n_cpus = 8, chains = 4, parallel_chains = 4,
              threads_per_chain = 2, reproducibility = "strict")
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 2
#>   Maximum requested workers: 8
#>   Reproducibility: strict
```
