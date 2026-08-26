# Compute expected value of sample information from nested simulation output

Aggregate a pre-posterior simulation in which each row of the
conditional expected-utility matrix represents one possible future
dataset after Bayesian updating.

## Usage

``` r
bayes_evsi(current_utility, conditional_expected_utility, information_cost = 0)
```

## Arguments

- current_utility:

  Current posterior utility draws by action.

- conditional_expected_utility:

  Matrix with one row per simulated future dataset and one column per
  action; cells are posterior expected utilities after that dataset.

- information_cost:

  Optional cost of collecting the sample information.

## Value

A one-row data frame containing EVSI, net EVSI, EVPI upper bound, and a
diagnostic indicating whether the Monte Carlo EVSI estimate exceeds
EVPI.

## Examples

``` r
# Example 1
set.seed(1)
cur <- cbind(A=rnorm(1000,10,2), B=rnorm(1000,11,3))
ceu <- cbind(A=rnorm(200,10,1), B=rnorm(200,11,1))
bayes_evsi(cur, ceu)
#>   current_expected_utility expected_utility_with_sample_information      evsi
#> 1                 10.95121                                 11.24871 0.2974989
#>   evpi_upper_bound information_cost  net_evsi evsi_exceeds_evpi_flag
#> 1         1.049804                0 0.2974989                  FALSE
# Example 2
bayes_evsi(cur,ceu,information_cost=.2)
#>   current_expected_utility expected_utility_with_sample_information      evsi
#> 1                 10.95121                                 11.24871 0.2974989
#>   evpi_upper_bound information_cost   net_evsi evsi_exceeds_evpi_flag
#> 1         1.049804              0.2 0.09749886                  FALSE
# Example 3
bayes_evsi(cur,ceu)$exceeds_evpi
#> NULL
```
