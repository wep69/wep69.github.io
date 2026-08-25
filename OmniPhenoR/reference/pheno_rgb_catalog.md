# Catalog of RGB and visible-band indices

Returns the formula registry used by
[`pheno_rgb_indices()`](https://wep69.github.io/OmniPhenoR/reference/pheno_rgb_indices.md).
The catalog distinguishes canonical names from aliases and records
whether a formula is scale invariant, uses unit-scaled RGB, or uses
byte-scaled RGB. This is important because some names are used
inconsistently across software.

## Usage

``` r
pheno_rgb_catalog(family = NULL)
```

## Arguments

- family:

  Optional regular expression used to filter index families.

## Value

A tibble with index metadata and formula text.

## References

Woebbecke DM, Meyer GE, Von Bargen K, Mortensen DA (1995). Color indices
for weed identification under various soil, residue, and lighting
conditions. *Transactions of the ASAE* 38(1):259-269.
[doi:10.13031/2013.27838](https://doi.org/10.13031/2013.27838) .

Gitelson AA, Kaufman YJ, Stark R, Rundquist D (2002). Novel algorithms
for remote estimation of vegetation fraction. *Remote Sensing of
Environment* 80(1):76-87.
[doi:10.1016/S0034-4257(01)00289-9](https://doi.org/10.1016/S0034-4257%2801%2900289-9)
.

Hunt ER Jr et al. (2013). A visible band index for remote sensing leaf
chlorophyll content at the canopy scale. *International Journal of
Applied Earth Observation and Geoinformation* 21:103-112.
[doi:10.1016/j.jag.2012.07.020](https://doi.org/10.1016/j.jag.2012.07.020)
.

## Examples

``` r
pheno_rgb_catalog()
#> # A tibble: 59 × 7
#>    index family formula                 scaling canonical alias_of reference_key
#>    <chr> <chr>  <chr>                   <chr>   <lgl>     <chr>    <chr>        
#>  1 R     basic  R                       native  TRUE      NA       NA           
#>  2 G     basic  G                       native  TRUE      NA       NA           
#>  3 B     basic  B                       native  TRUE      NA       NA           
#>  4 r     basic  R/(R+G+B)               invari… TRUE      NA       NA           
#>  5 g     basic  G/(R+G+B)               invari… TRUE      NA       NA           
#>  6 b     basic  B/(R+G+B)               invari… TRUE      NA       NA           
#>  7 INT   basic  (R+G+B)/3               native  TRUE      NA       NA           
#>  8 LUMA  basic  0.2126R+0.7152G+0.0722B unit    TRUE      NA       NA           
#>  9 Grey  basic  0.2898r+0.5870g+0.1140b invari… TRUE      NA       NA           
#> 10 BI    basic  sqrt((R^2+G^2+B^2)/3)   native  TRUE      NA       NA           
#> # ℹ 49 more rows
subset(pheno_rgb_catalog(), family == "vegetation")
#> # A tibble: 26 × 7
#>    index  family     formula            scaling canonical alias_of reference_key
#>    <chr>  <chr>      <chr>              <chr>   <lgl>     <chr>    <chr>        
#>  1 ExG    vegetation 2g-r-b             invari… TRUE      NA       NA           
#>  2 ExR    vegetation 1.4r-g             invari… TRUE      NA       NA           
#>  3 ExB    vegetation 1.4b-g             invari… TRUE      NA       NA           
#>  4 ExGR   vegetation 3g-2.4r-b          invari… TRUE      NA       NA           
#>  5 MxEG   vegetation 1.262G-0.884R-0.3… byte    TRUE      NA       NA           
#>  6 NGRDI  vegetation (G-R)/(G+R)        invari… TRUE      NA       NA           
#>  7 GRVI   vegetation (G-R)/(G+R)        invari… FALSE     NGRDI    NA           
#>  8 NGRDI3 vegetation (G-R)/(R+G+B)      invari… TRUE      NA       NA           
#>  9 NRGDI3 vegetation (R-G)/(R+G+B)      invari… TRUE      NA       NA           
#> 10 NRBDI3 vegetation (R-B)/(R+G+B)      invari… TRUE      NA       NA           
#> # ℹ 16 more rows
subset(pheno_rgb_catalog("difference|ratio"), canonical)
#> # A tibble: 12 × 7
#>    index family     formula scaling   canonical alias_of reference_key
#>    <chr> <chr>      <chr>   <chr>     <lgl>     <chr>    <chr>        
#>  1 GRD   difference G-R     native    TRUE      NA       NA           
#>  2 BGD   difference B-G     native    TRUE      NA       NA           
#>  3 RGD   difference R-G     native    TRUE      NA       NA           
#>  4 RBD   difference R-B     native    TRUE      NA       NA           
#>  5 GBD   difference G-B     native    TRUE      NA       NA           
#>  6 BRD   difference B-R     native    TRUE      NA       NA           
#>  7 GRRI  ratio      G/R     invariant TRUE      NA       NA           
#>  8 GBRI  ratio      G/B     invariant TRUE      NA       NA           
#>  9 RBRI  ratio      R/B     invariant TRUE      NA       NA           
#> 10 RGRI  ratio      R/G     invariant TRUE      NA       NA           
#> 11 BGRI  ratio      B/G     invariant TRUE      NA       NA           
#> 12 BRRI  ratio      B/R     invariant TRUE      NA       NA           
```
