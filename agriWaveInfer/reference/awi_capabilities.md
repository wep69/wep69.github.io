# Inspect computational capabilities and preflight an analysis

Reports optional engines and checks whether an intended wavelet,
driver-attribution, phase, or network operation is ready to run. The
report is a preflight diagnostic, not evidence that the numerical
analysis has been executed successfully.

## Usage

``` r
awi_capabilities()
awi_doctor(x,
  operation = c("cwt",
    "xwt",
    "wtc",
    "pwc",
    "mwc",
    "driver_screen",
    "driver_select",
    "phase",
    "directionality",
    "network",
    "spatial_coherence",
    "spatial_driver_map",
    "workflow"),
  y = NULL,
  others = list(),
  spec = awi_wavelet_spec(),
  backend = spec@backend,
  minimum_n = 32L,
  tolerance = 1e-6)
```

## Arguments

- x:

  Primary `awi_series`. For `"pwc"` and `"mwc"`, this is the response
  series in the preflight convention.

- operation:

  Requested operation.

- y:

  Second aligned series for XWT/WTC, or the focal predictor for PWC
  preflight.

- others:

  For PWC, a non-empty list of conditioning series; for MWC,
  driver-screen/selection, and network preflight, a non-empty list of
  predictor, candidate-driver, or additional node series.

- spec:

  An `AwiWaveletSpec`.

- backend:

  Requested transform backend.

- minimum_n:

  Minimum positive integer number of observations required by the
  preflight check.

- tolerance:

  Positive finite relative tolerance used when evaluating time-grid
  regularity and alignment.

## Value

A capability data frame or an `awi_doctor` report.
