# Make an explicit posterior-equivalence decision

A decision is returned only relative to the user-specified ROPE and
required posterior mass. The function does not claim universal
scientific equivalence.

## Usage

``` r
bayes_equivalence(fit, range, probability = 0.95,
                              factor = NULL, parameter = NULL)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- range:

  Numeric length-two ROPE.

- probability:

  Required posterior mass inside the ROPE.

- factor:

  Optional treatment factor.

- parameter:

  Optional parameter name.

## Value

A data frame with an equivalence decision.

## Examples

``` r
# Example 1: pairwise treatment equivalence
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 800)
bayes_equivalence(f, c(-0.20, 0.20), probability = 0.90, factor = "treatment")
#>         contrast     median      lower        upper      pd rope_pct
#> 1    BioA - BioB -0.4137717 -0.7941509  0.007868319 0.97000  0.14375
#> 2    BioA - BioC -0.7094940 -1.1024956 -0.300925574 0.99875  0.01125
#> 3 BioA - Control  0.2010917 -0.1682911  0.563676375 0.86750  0.48125
#> 4    BioB - BioC -0.2905560 -0.6756467  0.059672652 0.94625  0.31125
#> 5 BioB - Control  0.6105357  0.2251577  1.006307482 0.99750  0.02000
#> 6 BioC - Control  0.9064270  0.5380200  1.297460339 1.00000  0.00000
#>   required_probability equivalent_by_rule                 decision_rule
#> 1                  0.9              FALSE Posterior mass in ROPE >= 0.9
#> 2                  0.9              FALSE Posterior mass in ROPE >= 0.9
#> 3                  0.9              FALSE Posterior mass in ROPE >= 0.9
#> 4                  0.9              FALSE Posterior mass in ROPE >= 0.9
#> 5                  0.9              FALSE Posterior mass in ROPE >= 0.9
#> 6                  0.9              FALSE Posterior mass in ROPE >= 0.9

# Example 2: stricter posterior mass
bayes_equivalence(f, c(-0.20, 0.20), probability = 0.95, factor = "treatment")
#>         contrast     median      lower        upper      pd rope_pct
#> 1    BioA - BioB -0.4137717 -0.7941509  0.007868319 0.97000  0.14375
#> 2    BioA - BioC -0.7094940 -1.1024956 -0.300925574 0.99875  0.01125
#> 3 BioA - Control  0.2010917 -0.1682911  0.563676375 0.86750  0.48125
#> 4    BioB - BioC -0.2905560 -0.6756467  0.059672652 0.94625  0.31125
#> 5 BioB - Control  0.6105357  0.2251577  1.006307482 0.99750  0.02000
#> 6 BioC - Control  0.9064270  0.5380200  1.297460339 1.00000  0.00000
#>   required_probability equivalent_by_rule                  decision_rule
#> 1                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 2                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 3                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 4                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 5                 0.95              FALSE Posterior mass in ROPE >= 0.95
#> 6                 0.95              FALSE Posterior mass in ROPE >= 0.95

# Example 3: parameter-level equivalence
p <- setdiff(bayes_summary(f)$parameter, c("(Intercept)", "sigma"))[1]
bayes_equivalence(f, c(-0.10, 0.10), probability = 0.90, parameter = p)
#>       parameter    median         q2.5     q97.5 p_below_rope p_in_rope
#> 1 treatmentBioB 0.4137717 -0.007868319 0.7941509      0.00875    0.0625
#>   p_above_rope required_probability equivalent_by_rule
#> 1      0.92875                  0.9              FALSE
#>                   decision_rule
#> 1 Posterior mass in ROPE >= 0.9
```
