# Probability of a scientifically meaningful effect

Probability of a scientifically meaningful effect

## Usage

``` r
bayes_practical_significance(fit, threshold,
                                         factor = NULL, parameter = NULL)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- threshold:

  Non-negative absolute scientific threshold.

- factor:

  Optional treatment factor.

- parameter:

  Optional parameter name.

## Value

A data frame.

## Examples

``` r
# Example 1: treatment differences larger than 0.30 yield units
f <- bayes_fit(bayes_rcbd(bayes_teaching_data("rcbd"), "yield",
               "cultivar", "block"), draws = 800)
bayes_practical_significance(f, 0.30, factor = "cultivar")
#>    contrast       median        lower        upper      pd p_abs_gt_meaningful
#> 1   C1 - C2 -0.384868323 -0.791186734  0.003606751 0.97375             0.68000
#> 2   C1 - C3 -0.810589549 -1.225427094 -0.395566317 1.00000             0.99000
#> 3   C1 - C4 -0.784620837 -1.207363319 -0.361256624 1.00000             0.98625
#> 4   C1 - C5 -0.394882279 -0.792597024 -0.006986029 0.97625             0.64250
#> 5   C2 - C3 -0.417204529 -0.844069788 -0.013771983 0.97625             0.71250
#> 6   C2 - C4 -0.394478812 -0.810004322 -0.012520766 0.97750             0.67750
#> 7   C2 - C5  0.002455417 -0.440808100  0.435628076 0.50250             0.16625
#> 8   C3 - C4  0.032687470 -0.406918931  0.452346212 0.55000             0.17000
#> 9   C3 - C5  0.421104689 -0.006725293  0.822120913 0.97250             0.70500
#> 10  C4 - C5  0.386503822 -0.010771748  0.842779771 0.97250             0.65875
#>    p_gt_meaningful p_lt_minus_meaningful
#> 1          0.00250               0.67750
#> 2          0.00000               0.99000
#> 3          0.00000               0.98625
#> 4          0.00000               0.64250
#> 5          0.00000               0.71250
#> 6          0.00000               0.67750
#> 7          0.08375               0.08250
#> 8          0.10375               0.06625
#> 9          0.70375               0.00125
#> 10         0.65625               0.00250

# Example 2: a more demanding threshold
bayes_practical_significance(f, 0.50, factor = "cultivar")
#>    contrast       median        lower        upper      pd p_abs_gt_meaningful
#> 1   C1 - C2 -0.384868323 -0.791186734  0.003606751 0.97375             0.28375
#> 2   C1 - C3 -0.810589549 -1.225427094 -0.395566317 1.00000             0.93750
#> 3   C1 - C4 -0.784620837 -1.207363319 -0.361256624 1.00000             0.90750
#> 4   C1 - C5 -0.394882279 -0.792597024 -0.006986029 0.97625             0.30875
#> 5   C2 - C3 -0.417204529 -0.844069788 -0.013771983 0.97625             0.35250
#> 6   C2 - C4 -0.394478812 -0.810004322 -0.012520766 0.97750             0.29625
#> 7   C2 - C5  0.002455417 -0.440808100  0.435628076 0.50250             0.02500
#> 8   C3 - C4  0.032687470 -0.406918931  0.452346212 0.55000             0.02625
#> 9   C3 - C5  0.421104689 -0.006725293  0.822120913 0.97250             0.35500
#> 10  C4 - C5  0.386503822 -0.010771748  0.842779771 0.97250             0.31125
#>    p_gt_meaningful p_lt_minus_meaningful
#> 1          0.00000               0.28375
#> 2          0.00000               0.93750
#> 3          0.00000               0.90750
#> 4          0.00000               0.30875
#> 5          0.00000               0.35250
#> 6          0.00000               0.29625
#> 7          0.00875               0.01625
#> 8          0.01875               0.00750
#> 9          0.35500               0.00000
#> 10         0.31125               0.00000

# Example 3: coefficient-level practical relevance
p <- setdiff(bayes_summary(f)$parameter, c("(Intercept)", "sigma"))[1]
bayes_practical_significance(f, 0.25, parameter = p)
#>    parameter threshold p_abs_gt_threshold p_gt_threshold p_lt_minus_threshold
#> 1 cultivarC2      0.25            0.78125        0.77625                0.005
```
