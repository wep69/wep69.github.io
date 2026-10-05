# Compare crop profile coverage

Compare crop profile coverage

## Usage

``` r
crop_compare_profiles(...)
```

## Arguments

- ...:

  Two or more crop profiles, or one list of crop profiles.

## Value

An \`agri_crop_comparison\` data.frame indicating which criteria are
present.

## Examples

``` r
lib <- crop_profile_library(c("maize", "sorghum", "bean"))
crop_compare_profiles(lib)
#> <agri_crop_comparison>
#>  criterion mz_maize_rainfed mz_sorghum_rainfed mz_bean_rainfed
#>        AWC             TRUE               TRUE            TRUE
#>         CV             TRUE               TRUE            TRUE
#>        LGP             TRUE               TRUE            TRUE
#>    Pseason             TRUE               TRUE            TRUE
#>        SOC             TRUE               TRUE            TRUE
#>    Tseason             TRUE               TRUE            TRUE
#>      Twarm             TRUE              FALSE            TRUE
#>         pH             TRUE               TRUE            TRUE
#>      slope             TRUE               TRUE            TRUE
```
