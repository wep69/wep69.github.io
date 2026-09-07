# Create a crop-stage calendar and align a time series to it

Defines explicit crop-stage intervals and maps observations to those
intervals while retaining the original time coordinate.

## Usage

``` r
awi_crop_stage(stage, start, end, cycle = 1L,
  crop = NA_character_, cultivar = NA_character_, source = "user",
  uncertainty = 0, metadata = list(), spec = NULL)
awi_stage_align(x, stages, spec = attr(stages, "spec"))
```

## Arguments

- stage:

  Character vector naming each phenological interval.

- start,end:

  Matching numeric, Date, or POSIXt interval boundaries.

- cycle:

  Crop-cycle identifier, recycled if scalar.

- crop,cultivar:

  Optional crop and cultivar identifiers.

- source:

  Source of the stage information, for example field observation, model,
  remote sensing, or user definition.

- uncertainty:

  Non-negative boundary uncertainty in the native time units.

- metadata:

  Named or unnamed list of additional provenance.

- spec:

  An `AwiPhenologySpec`.

- x:

  An `awi_series` to annotate.

- stages:

  An `awi_crop_stage` calendar.

## Value

`awi_crop_stage()` returns an `awi_crop_stage` data frame.
`awi_stage_align()` returns an `awi_stage_aligned` data frame containing
physical time, value, stage, cycle, within-stage fraction, and a
boundary-uncertainty flag.

## Details

The default interval policy is left-closed/right-open, with the last
interval in a cycle including its right boundary. Overlapping intervals
are rejected by default. The alignment object records counts and
eligibility by stage but does not alter the original series values or
time axis.
