# Build an uncertainty ensemble from a score matrix

Fast constructor for ensembles whose members are already scored
(seasons, scenarios, parameter sets): it avoids building one
\`agri_suitability\` object per member. The result works with
\`uncertainty_summary()\`, \`class_probability()\`,
\`class_stability()\`, \`uncertainty_decompose()\` and \`suit_risk()\`.

## Usage

``` r
ensemble_from_scores(
  scores,
  member_ids = NULL,
  weights = NULL,
  design = NULL,
  kind = "interannual",
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  template = NULL,
  cells = NULL,
  units = NULL
)
```

## Arguments

- scores:

  Numeric matrix with units in rows and members in columns, or a
  \`SpatRaster\` with one layer per member.

- member_ids:

  Member identifiers; default column or layer names, or sequential
  labels when those are missing or duplicated.

- weights:

  Optional member weights (normalised).

- design:

  Optional data frame describing each member (one row per member),
  stored for \`uncertainty_decompose()\`.

- kind:

  Free label stored in the ensemble (\`interannual\`, \`scenario\`...).

- breaks, labels:

  Class definition stored with the ensemble.

- template, cells:

  Optional raster template and cell numbers when rows are raster cells,
  used by \`suit_risk(as_raster = TRUE)\`.

- units:

  Optional unit (row) labels.

## Value

An \`agri_uncertainty_ensemble\`.

## Examples

``` r
set.seed(5)
sc <- matrix(runif(30), nrow = 3, dimnames = list(c("A", "B", "C"), NULL))
e <- ensemble_from_scores(sc, design = data.frame(year = factor(1991:2000)))
uncertainty_summary(e)$statistics
#>        mean        sd       p05       p50       p95 n_available
#> A 0.4024878 0.2672288 0.1104530 0.2843995 0.9659641          10
#> B 0.4493135 0.2561898 0.1046501 0.3875257 0.8421794          10
#> C 0.6723725 0.2776310 0.2257173 0.7010575 0.9565001          10
class_probability(e)$probability
#>   P_N P_S3 P_S2 P_S1
#> A 0.4  0.2  0.3  0.1
#> B 0.3  0.3  0.2  0.2
#> C 0.1  0.3  0.1  0.5
```
