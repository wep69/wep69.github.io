# Define an access policy

Define an access policy

## Usage

``` r
pheno_access_policy(
  policy = c("open", "embargoed", "restricted", "private"),
  conditions = "",
  embargo_until = as.Date(NA)
)
```

## Arguments

- policy:

  One of open, embargoed, restricted, private.

- conditions:

  Human-readable conditions.

- embargo_until:

  Optional date.

## Value

`pheno_access_policy`.

## Examples

``` r
pheno_access_policy("open")
#> $policy
#> [1] "open"
#> 
#> $conditions
#> [1] ""
#> 
#> $embargo_until
#> [1] NA
#> 
#> attr(,"class")
#> [1] "pheno_access_policy"
pheno_access_policy("embargoed","release after publication",as.Date("2027-01-01"))
#> $policy
#> [1] "embargoed"
#> 
#> $conditions
#> [1] "release after publication"
#> 
#> $embargo_until
#> [1] "2027-01-01"
#> 
#> attr(,"class")
#> [1] "pheno_access_policy"
pheno_access_policy("restricted","request access from data steward")
#> $policy
#> [1] "restricted"
#> 
#> $conditions
#> [1] "request access from data steward"
#> 
#> $embargo_until
#> [1] NA
#> 
#> attr(,"class")
#> [1] "pheno_access_policy"
```
