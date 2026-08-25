# Run frozen scientific validation checks

Executes deterministic software checks on frozen synthetic scenarios.
Version 0.2.0 adds group-split leakage checks, model-probability
segmentation, uncertainty ordering, overlap-aware tiling, and expanded
texture ground truth. These are implementation checks, not claims of
field accuracy.

## Usage

``` r
pheno_validate(
  scope = c("all", "rgb", "morphology", "disease", "texture", "ml", "tiling")
)
```

## Arguments

- scope:

  `"all"`, `"rgb"`, `"morphology"`, `"disease"`, `"texture"`, `"ml"`, or
  `"tiling"`.

## Value

A tibble with check, estimate, target, tolerance, and pass columns.

## Examples

``` r
pheno_validate("rgb")
#> # A tibble: 3 × 6
#>   domain check                  estimate target tolerance pass 
#>   <chr>  <chr>                     <dbl>  <dbl>     <dbl> <lgl>
#> 1 rgb    NGRDI scale invariance      0.5    0.5     1e-12 TRUE 
#> 2 rgb    GLI scale invariance        0.6    0.6     1e-12 TRUE 
#> 3 rgb    ExG scale invariance        1      1       1e-12 TRUE 
pheno_validate("ml")
#> # A tibble: 4 × 6
#>   domain check                                   estimate target tolerance pass 
#>   <chr>  <chr>                                      <dbl>  <dbl>     <dbl> <lgl>
#> 1 ml     function-model perfect Dice                    1      1         0 TRUE 
#> 2 ml     function-model perfect IoU                     1      1         0 TRUE 
#> 3 ml     ambiguous probability has higher entro…        1      1         0 TRUE 
#> 4 ml     group split has zero leakage                   0      0         0 TRUE 
pheno_validate("all")
#> # A tibble: 21 × 6
#>    domain     check                      estimate target tolerance pass 
#>    <chr>      <chr>                         <dbl>  <dbl>     <dbl> <lgl>
#>  1 rgb        NGRDI scale invariance         0.5     0.5     1e-12 TRUE 
#>  2 rgb        GLI scale invariance           0.6     0.6     1e-12 TRUE 
#>  3 rgb        ExG scale invariance           1       1       1e-12 TRUE 
#>  4 morphology square area                  100     100       0     TRUE 
#>  5 morphology square perimeter              40      40       0     TRUE 
#>  6 morphology frozen rectangle area       1200    1200       0     TRUE 
#>  7 disease    known 20 percent severity     20      20       0     TRUE 
#>  8 disease    frozen severity 5 percent      5.00    5       5e- 2 TRUE 
#>  9 disease    frozen severity 10 percent    10.0    10       5e- 2 TRUE 
#> 10 disease    frozen severity 25 percent    25.0    25       5e- 2 TRUE 
#> # ℹ 11 more rows
```
