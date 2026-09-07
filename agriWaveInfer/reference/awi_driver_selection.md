# Screen and select multiscale drivers with explicit parsimony rules

Implements reproducible single-driver screening, forward coherence
elimination and bounded exhaustive MWC subset selection.

## Usage

``` r
awi_driver_screen(y, drivers, spec = awi_wavelet_spec(),
  condition = awi_condition_spec(method = "svd"), null = awi_null_spec(),
  driver_spec = awi_driver_spec(), period_range = NULL, time_range = NULL,
  quiet = TRUE)

awi_coherence_elimination(y, drivers, spec = awi_wavelet_spec(),
  condition = awi_condition_spec(method = "svd"), null = awi_null_spec(),
  driver_spec = awi_driver_spec(strategy = "cea"),
  period_range = NULL, time_range = NULL, quiet = TRUE)

awi_mwc_select(y, drivers, spec = awi_wavelet_spec(),
  condition = awi_condition_spec(method = "svd"), null = awi_null_spec(),
  driver_spec = awi_driver_spec(), period_range = NULL, time_range = NULL,
  quiet = TRUE)
```

## Arguments

- y:

  Response `awi_series`.

- drivers:

  One driver series or a named list of aligned `awi_series` objects.

- spec:

  An `AwiWaveletSpec`.

- condition:

  An `AwiConditionSpec`; SVD regularization is the default for
  attribution workflows.

- null:

  An `AwiNullSpec`. Required when PoSC or conditional PWC is evaluated.

- driver_spec:

  An `AwiDriverSpec` defining metric, gain threshold, strategy and
  numerical eligibility rules.

- period_range:

  Optional period band used consistently for all candidate comparisons.

- time_range:

  Optional temporal window used consistently for all candidate
  comparisons.

- quiet:

  Suppress progress output.

## Value

`awi_driver_screen()` returns an `awi_driver_screen` object with ranked
single-driver metrics and numerical diagnostics. Selection functions
return an `awi_driver_selection` containing the selected set, candidate
history or exhaustive-combination table, selected MWC result, policy and
optional conditional-support table.

## Details

The forward CEA starts from the best eligible single driver and retains
an added driver only when the configured metric improves by at least
`min_gain`. Exhaustive selection evaluates all subsets up to
`max_drivers`, subject to `max_combinations`, and retains multi-driver
sets only when they clear the gain threshold relative to the best single
driver. Local condition number, rank, instability and regularization
diagnostics are carried into eligibility decisions. When requested, the
final set is followed by improved PWC checks of each driver conditional
on the others. These are association and attribution diagnostics, not
causal-effect estimators.

## References

Mohan MG, Fathima S, Adarsh S, Baiju N, Nair GRA, Meenakshi S, Krishnan
MS (2023). Analyzing the streamflow teleconnections of greater Pampa
basin, Kerala, India using wavelet coherence. Physics and Chemistry of
the Earth, Parts A/B/C 131, 103446. doi:10.1016/j.pce.2023.103446.

Sankaran A et al. (2026). Wavelet-based assessment of climate
oscillation and meteorological influences on cereal yield variability
across India's agro-climatic zones. Theoretical and Applied Climatology
157, 147. doi:10.1007/s00704-026-06085-2.
