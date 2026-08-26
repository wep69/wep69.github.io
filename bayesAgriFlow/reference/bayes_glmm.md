# Specify a generalized linear mixed model

Specify a generalized linear mixed model

## Usage

``` r
bayes_glmm(data, response, fixed, group,
                       family = c("bernoulli", "binomial", "poisson",
                                  "negbinomial", "gamma", "lognormal", "beta"),
                       random_slopes = NULL, nested = NULL, crossed = NULL,
                       trials = NULL, exposure = NULL, link = NULL)
```

## Arguments

- data:

  A data frame.

- response:

  Response column.

- fixed:

  One-sided fixed-effects formula or character vector.

- group:

  Character vector of random-intercept grouping factors.

- family:

  One of \`"bernoulli"\`, \`"binomial"\`, \`"poisson"\`,
  \`"negbinomial"\`, \`"gamma"\`, \`"lognormal"\`, or \`"beta"\`.

- random_slopes:

  Optional named list of random slopes.

- nested:

  Optional nested grouping chains.

- crossed:

  Optional crossed grouping factors.

- trials:

  Binomial trials column.

- exposure:

  Positive exposure column for count-rate models.

- link:

  Optional link override.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: negative-binomial insect counts with random block effects
dc <- bayes_teaching_data("glmm_count")
bayes_glmm(dc, "insects", ~ treatment, "block", family = "negbinomial")
#> <bayes_design>
#>   Type: Generalized linear mixed model
#>   Response: insects
#>   Family: Negative binomial
#>   Observations: 72
#>   fixed: treatment
#>   group: block

# Example 2: binomial disease incidence
db <- bayes_teaching_data("glmm_binomial")
bayes_glmm(db, "infected", ~ treatment, "block", family = "binomial",
           trials = "plants")
#> <bayes_design>
#>   Type: Generalized linear mixed model
#>   Response: infected
#>   Family: Binomial
#>   Observations: 48
#>   fixed: treatment
#>   group: block
#>   trials: plants

# Example 3: Poisson model with an exposure offset
dc$exposure <- 10
bayes_glmm(dc, "insects", ~ treatment, "block", family = "poisson",
           exposure = "exposure")
#> <bayes_design>
#>   Type: Generalized linear mixed model
#>   Response: insects
#>   Family: Poisson
#>   Observations: 72
#>   fixed: treatment
#>   group: block
#>   exposure: exposure
```
