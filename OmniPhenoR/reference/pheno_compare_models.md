# Compare model performance summaries

Compare model performance summaries

## Usage

``` r
pheno_compare_models(metrics, benchmark = NULL)
```

## Arguments

- metrics:

  Named list of one-row metric tables.

- benchmark:

  Optional benchmark table from
  [`pheno_benchmark()`](https://wep69.github.io/OmniPhenoR/reference/pheno_benchmark.md).

## Value

Combined comparison table/list.

## Examples

``` r
pheno_compare_models(list(a=data.frame(dice=.9),b=data.frame(dice=.85)))
#> # A tibble: 2 × 2
#>   model  dice
#>   <chr> <dbl>
#> 1 a      0.9 
#> 2 b      0.85
pheno_compare_models(list(a=data.frame(iou=.8),b=data.frame(iou=.7)),
  benchmark=data.frame(method=c("a","b"),elapsed_sec=c(.1,.2)))
#> $metrics
#> # A tibble: 2 × 2
#>   model   iou
#>   <chr> <dbl>
#> 1 a       0.8
#> 2 b       0.7
#> 
#> $benchmark
#> # A tibble: 2 × 2
#>   method elapsed_sec
#>   <chr>        <dbl>
#> 1 a              0.1
#> 2 b              0.2
#> 
pheno_compare_models(list(native=data.frame(mae=.1), external=data.frame(mae=.12)))
#> # A tibble: 2 × 2
#>   model      mae
#>   <chr>    <dbl>
#> 1 native    0.1 
#> 2 external  0.12
```
