# Specify memory-aware spatial wavelet execution

Creates a validated execution contract for chunked spatial wavelet
analysis and optional spatial output interoperability.

## Usage

``` r
awi_spatial_spec(chunk_size = 32L, max_memory_mb = 1024,
  parallel = FALSE, workers = 1L, retain_fields = FALSE,
  min_valid_fraction = 0.2, on_error = c("stop", "record"),
  crs = NA_character_)
```

## Arguments

- chunk_size:

  Requested number of spatial locations processed per chunk. The
  effective chunk may be reduced by the memory guard.

- max_memory_mb:

  Approximate memory budget in MB used for conservative chunk planning
  and optional field retention.

- parallel:

  Whether chunks may be evaluated with base R PSOCK workers.

- workers:

  Positive integer number of workers when parallel execution is enabled.

- retain_fields:

  Whether complete per-location time-frequency fields are retained. The
  default is `FALSE` to avoid unnecessary memory growth.

- min_valid_fraction:

  Minimum fraction of the requested time-period domain required for a
  location to be classified as adequately supported.

- on_error:

  Whether a failing location stops the analysis or is recorded as an
  error row.

- crs:

  Optional coordinate reference system string propagated to spatial
  outputs.

## Value

An S7 `AwiSpatialSpec` object.

## Details

The memory planner estimates the active time-frequency working set
conservatively and can reduce the requested chunk size. Retaining all
wavelet fields is opt-in because spatial inference should normally
summarize requested bands and discard unnecessary coefficients.
