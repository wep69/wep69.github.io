# Create a formal wavelet specification

Creates an S7 `AwiWaveletSpec`. Version 0.2.0 deliberately limits the
validated wavelet family to Morlet.

## Usage

``` r
awi_wavelet_spec(mother = "morlet", omega0 = 6, dj = 1/12, s0 = NA_real_,
  max_scale = NA_real_, padding = TRUE,
  normalization = c("backend", "bias_corrected"),
  coi_policy = c("retain", "flag"), backend = c("auto", "biwavelet"))
```

## Arguments

- mother:

  Wavelet family. Version 0.2.0 validates `"morlet"` only.

- omega0:

  Positive finite Morlet nondimensional frequency parameter.

- dj:

  Positive finite scale-resolution step.

- s0:

  `NA` for the backend-compatible default or one positive finite
  smallest scale.

- max_scale:

  `NA` for the backend-compatible default or one positive finite maximum
  scale.

- padding:

  Logical scalar controlling padding.

- normalization:

  Which exposed power surface is selected by `power_selected`:
  backend-native or available bias-corrected power.

- coi_policy:

  `"retain"` keeps the COI coordinates only; `"flag"` also returns a
  logical `coi_valid` mask.

- backend:

  `"auto"` or the currently implemented `"biwavelet"` transform backend.

## Value

An S7 `AwiWaveletSpec` object.
