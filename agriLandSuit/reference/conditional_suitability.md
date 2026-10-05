# Suitability conditioned on a climate mode

For each unit, compares member scores in two groups of seasons (for
example warm and cold terciles of Mozambique Channel SST): difference of
means with a two-sided permutation test, probability of S2 or better in
each group, and a linear regression of the yearly score on the
standardised index with optional standardised controls (for example Nino
3.4). P values are adjusted across units with the Benjamini-Hochberg
false discovery rate.

## Usage

``` r
conditional_suitability(
  scores,
  index,
  groups = NULL,
  contrast = c("warm", "cold"),
  controls = NULL,
  n_perm = 9999L,
  seed = NULL,
  threshold = 0.5,
  fdr = "BH"
)
```

## Arguments

- scores:

  Matrix of member scores (units x seasons) or an
  \`agri_uncertainty_ensemble\` with matrix draws.

- index:

  Numeric index, one value per season (same order as columns).

- groups:

  Optional factor of season groups; default \`tercile_groups(index)\`.

- contrast:

  Two group labels; the difference is \`contrast\[1\] - contrast\[2\]\`.

- controls:

  Optional data frame of control covariates, one row per season.

- n_perm:

  Number of permutations.

- seed:

  Optional seed.

- threshold:

  Score defining S2 or better.

- fdr:

  Multiple-testing adjustment passed to \`stats::p.adjust()\`.

## Value

An \`agri_conditional_suitability\` data frame.

## Details

Labels are permuted only among the seasons of the two compared groups.
When \`seed\` is \`NULL\` the current random stream is used, which
allows several calls (one per crop) to reproduce a single seeded loop.

## Examples

``` r
set.seed(3)
idx <- rnorm(24); n34 <- rnorm(24)
sc <- rbind(A = pmin(1, pmax(0, 0.5 + 0.2 * idx + rnorm(24, 0, 0.1))), B = runif(24))
conditional_suitability(sc, idx, controls = data.frame(N34 = n34), n_perm = 199, seed = 1)
#>   unit mean_warm mean_cold      delta p_perm P_S2plus_warm P_S2plus_cold
#> 1    A 0.6618504 0.2764755  0.3853749  0.005         1.000          0.00
#> 2    B 0.4270640 0.6720622 -0.2449982  0.100         0.375          0.75
#>    beta_index      p_index     beta_N34     p_N34 p_adj
#> 1  0.17048835 1.299384e-08  0.003217383 0.8674740  0.01
#> 2 -0.08577894 1.845877e-01 -0.050181780 0.4312226  0.10
```
