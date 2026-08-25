# Quantify symptomatic leaf area

Calculates lesion area and severity from explicit leaf and lesion masks.
When only an RGB image is supplied, leaf and lesion masks can be
generated from user-declared RGB indices and thresholds. The
explicit-mask pathway is the preferred validation reference because it
does not hide classification rules.

## Usage

``` r
pheno_disease(
  x = NULL,
  leaf_mask = NULL,
  lesion_mask = NULL,
  leaf_index = "ExG",
  leaf_threshold = "otsu",
  lesion_index = "ExR",
  lesion_threshold = NULL,
  lesion_direction = c("above", "below")
)
```

## Arguments

- x:

  Optional RGB image.

- leaf_mask:

  Optional binary leaf mask.

- lesion_mask:

  Optional binary lesion mask.

- leaf_index:

  Index used if `leaf_mask` is absent.

- leaf_threshold:

  Threshold used to obtain the leaf mask.

- lesion_index:

  Index used if `lesion_mask` is absent.

- lesion_threshold:

  Numeric threshold for lesion score. If `NULL`, Otsu is computed inside
  the leaf mask.

- lesion_direction:

  `"above"` or `"below"` the lesion threshold.

## Value

A `pheno_disease` list with masks and severity summary.

## Examples

``` r
d1 <- pheno_disease(
  leaf_mask = pheno_data("leaf_mask"),
  lesion_mask = pheno_data("lesion_mask")
)
d1$summary
#> # A tibble: 1 × 4
#>   leaf_area_px lesion_area_px healthy_area_px severity_percent
#>          <int>          <int>           <int>            <dbl>
#> 1         2872            317            2555             11.0
img <- pheno_data("leaf_rgb")
d2 <- pheno_disease(img, lesion_index = "ExR", lesion_threshold = .15)
d2$summary
#> # A tibble: 1 × 4
#>   leaf_area_px lesion_area_px healthy_area_px severity_percent
#>          <int>          <int>           <int>            <dbl>
#> 1         2872            317            2555             11.0
lm <- matrix(TRUE, 10, 10)
sm <- matrix(FALSE, 10, 10)
sm[1:2, ] <- TRUE
pheno_disease(leaf_mask = lm, lesion_mask = sm)$summary
#> # A tibble: 1 × 4
#>   leaf_area_px lesion_area_px healthy_area_px severity_percent
#>          <int>          <int>           <int>            <dbl>
#> 1          100             20              80               20
```
