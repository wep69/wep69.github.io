# Extract suitability classification

Extract suitability classification

## Usage

``` r
suitability_class(x)
```

## Arguments

- x:

  An \`agri_suitability\` or \`agri_suitability_class\` object.

## Value

Classification object or NULL if not yet classified.

## Examples

``` r
s <- suit_aggregate(cbind(rain = c(0.9, 0.6, 0.3, 0.1), temp = c(1, 0.7, 0.8, 0.5)))
suitability_class(suit_classify(s))
#> <agri_suitability_class>
#>  code label lower upper
#>     1     N  0.00  0.25
#>     2    S3  0.25  0.50
#>     3    S2  0.50  0.75
#>     4    S1  0.75  1.00
```
