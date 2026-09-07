# Specify a driver-attribution and parsimony policy

Creates a validated scientific contract for driver screening, gain-loss
analysis, redundancy-aware multivariate selection, and optional
conditional-support checks.

## Usage

``` r
awi_driver_spec(
  metric = c("posc", "awc"), min_gain = NULL, max_drivers = 4L,
  strategy = c("cea", "exhaustive"), weighting = c("area", "cell"),
  max_unstable_fraction = 0.25, min_valid_fraction = 0.25,
  conditional_check = c("none", "pwc"), min_conditional_posc = 0,
  max_combinations = 500L
)
```

## Arguments

- metric:

  Primary selection metric. `"posc"` uses Percentage of Significant
  Coherence; `"awc"` uses Average Wavelet Coherence.

- min_gain:

  Minimum improvement needed to retain additional drivers. If omitted,
  the default is 5 percentage points for PoSC and 0.05 coherence units
  for AWC.

- max_drivers:

  Maximum size of a retained predictor set.

- strategy:

  `"cea"` for forward Coherence Elimination Approach or `"exhaustive"`
  for bounded subset comparison.

- weighting:

  `"area"` approximates the time-period integral using cell widths;
  `"cell"` gives every lattice cell equal weight.

- max_unstable_fraction:

  Maximum permitted fraction of the requested domain flagged as locally
  singular or ill-conditioned.

- min_valid_fraction:

  Minimum fraction of cells within the requested time-period domain that
  must remain valid after finite-value and COI filtering.

- conditional_check:

  Whether the final selected set is followed by improved partial wavelet
  coherence checks for each selected driver conditional on the others.

- min_conditional_posc:

  Optional reporting threshold for conditional PoSC. A default of zero
  reports support without imposing an additional rejection rule.

- max_combinations:

  Hard cap on combinations evaluated by exhaustive selection.

## Value

An S7 object of class `AwiDriverSpec`.

## Details

The 5-percentage-point PoSC default follows a recent crop-climate
Coherence Elimination Approach, but it is treated as a configurable
parsimony rule rather than a universal significance threshold. Numerical
conditioning and conditional PWC are reported separately because an
increase in MWC or PoSC does not by itself demonstrate an independent
contribution from the added predictor.

## References

Sankaran A, Fazal F, Johnson A, Sridhar V, Prasad D, Abdul Rasheed AF
(2026). Wavelet-based assessment of climate oscillation and
meteorological influences on cereal yield variability across India's
agro-climatic zones. Theoretical and Applied Climatology 157, 147.
doi:10.1007/s00704-026-06085-2.
