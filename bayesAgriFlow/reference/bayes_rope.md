# Region of practical equivalence assessment

ROPE limits must be supplied in scientifically meaningful units. Version
0.3.0 deliberately has no universal automatic ROPE for treatment
contrasts.

## Usage

``` r
bayes_rope(fit, range, factor = NULL, parameter = NULL)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- range:

  Numeric length-two ROPE.

- factor:

  Optional treatment factor. If supplied, all pairwise contrasts are
  evaluated.

- parameter:

  Optional exact posterior parameter name.

## Value

A data frame.

## Examples

``` r
# Example 1: treatment-contrast ROPE
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 800)
bayes_rope(f, c(-0.20, 0.20), factor = "treatment")
#>         contrast     median      lower        upper      pd rope_pct
#> 1    BioA - BioB -0.4137717 -0.7941509  0.007868319 0.97000  0.14375
#> 2    BioA - BioC -0.7094940 -1.1024956 -0.300925574 0.99875  0.01125
#> 3 BioA - Control  0.2010917 -0.1682911  0.563676375 0.86750  0.48125
#> 4    BioB - BioC -0.2905560 -0.6756467  0.059672652 0.94625  0.31125
#> 5 BioB - Control  0.6105357  0.2251577  1.006307482 0.99750  0.02000
#> 6 BioC - Control  0.9064270  0.5380200  1.297460339 1.00000  0.00000

# Example 2: narrower scientific equivalence region
bayes_rope(f, c(-0.10, 0.10), factor = "treatment")
#>         contrast     median      lower        upper      pd rope_pct
#> 1    BioA - BioB -0.4137717 -0.7941509  0.007868319 0.97000  0.06250
#> 2    BioA - BioC -0.7094940 -1.1024956 -0.300925574 0.99875  0.00375
#> 3 BioA - Control  0.2010917 -0.1682911  0.563676375 0.86750  0.22625
#> 4    BioB - BioC -0.2905560 -0.6756467  0.059672652 0.94625  0.13125
#> 5 BioB - Control  0.6105357  0.2251577  1.006307482 0.99750  0.00750
#> 6 BioC - Control  0.9064270  0.5380200  1.297460339 1.00000  0.00000

# Example 3: coefficient-level ROPE
p <- setdiff(bayes_summary(f)$parameter, c("(Intercept)", "sigma"))[1]
bayes_rope(f, c(-0.10, 0.10), parameter = p)
#>       parameter    median         q2.5     q97.5 p_below_rope p_in_rope
#> 1 treatmentBioB 0.4137717 -0.007868319 0.7941509      0.00875    0.0625
#>   p_above_rope
#> 1      0.92875
```
