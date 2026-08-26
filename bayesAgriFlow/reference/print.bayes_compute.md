# Print a bayes_compute object

Compact console display for \`bayes_compute\` objects: backend, CPU
budget, chain parallelism, threading, and reproducibility mode.

## Usage

``` r
# S3 method for class 'bayes_compute'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_compute\`: computation configuration.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
bayes_compute(n_cpus = 4, chains = 4)
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 4
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
```
