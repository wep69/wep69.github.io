# Inspect computation and optional backend availability

Inspect computation and optional backend availability

## Usage

``` r
bayes_compute_info(compute = NULL)
```

## Arguments

- compute:

  Optional \`bayes_compute\` object.

## Value

A named list.

## Examples

``` r
# Example 1
bayes_compute_info()
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
#> 
# Example 2
bayes_compute_info(bayes_compute(n_cpus = 2, chains = 2))
#> $compute
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 2
#>   Chains: 2
#>   Parallel chains: 2
#>   Threads per chain: 1
#>   Maximum requested workers: 2
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
#> 
# Example 3
bayes_compute_info(bayes_compute(backend = "rstan", n_cpus = 2, chains = 2))
#> $compute
#> <bayes_compute>
#>   Backend: rstan
#>   CPU budget: 2
#>   Chains: 2
#>   Parallel chains: 2
#>   Threads per chain: 1
#>   Maximum requested workers: 2
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
#> 
```
