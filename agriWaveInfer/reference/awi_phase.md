# Extract phase and convert it to multiscale lead-lag summaries

Converts wavelet phase into explicitly filtered, circularly summarized
lead-lag information.

## Usage

``` r
awi_phase(x, phase_spec = awi_phase_spec(), period_range = NULL, time_range = NULL)
awi_phase_lag(x, phase_spec = awi_phase_spec(), period_range = NULL, time_range = NULL)
awi_lag_band(x, period_range, time_range = NULL, phase_spec = awi_phase_spec())
awi_lead_map(x, phase_spec = awi_phase_spec(), period_range = NULL, time_range = NULL)
```

## Arguments

- x:

  An `awi_wavelet_result` containing coherence and phase, or for
  `awi_phase_lag()` an `awi_phase` object.

- phase_spec:

  An `AwiPhaseSpec` object controlling significance, coherence, COI,
  weighting, and ambiguity rules.

- period_range:

  Optional two-element period band. Required by `awi_lag_band()`.

- time_range:

  Optional two-element time window.

## Value

`awi_phase()` returns an `awi_phase` object; `awi_phase_lag()` returns
an `awi_phase_lag`; `awi_lag_band()` returns a circular band summary;
`awi_lead_map()` returns a time-period map of principal-branch lags and
direction classes.

## Details

For the stored phase convention, positive phase means the first series
leads the second and negative phase means the second leads the first.
The signed principal lag is `phase * period / (2*pi)`, constrained to
plus or minus half a period. This representation is cycle-ambiguous by
construction and is not a causal effect. Band summaries report circular
mean phase, resultant length, circular spread, weighted median cell lag,
persistence, and anti-phase ambiguity.

## References

Grinsted A, Moore JC, Jevrejeva S (2004). Application of the cross
wavelet transform and wavelet coherence to geophysical time series.
Nonlinear Processes in Geophysics 11:561-566.
doi:10.5194/npg-11-561-2004.

Funashima Y (2017). Time-varying leads and lags across frequencies using
a continuous wavelet transform approach. Economic Modelling 60:24-28.
doi:10.1016/j.econmod.2016.08.024.
