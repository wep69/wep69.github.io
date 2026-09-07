# Define and harmonize an ordered drought-propagation chain

Prevents sign conventions of drought, vegetation, soil-moisture, or
yield indicators from being mistaken for phase reversals in a
propagation analysis.

## Usage

``` r
awi_drought_chain(series, roles = names(series), orientation = "wet_positive",
  target_orientation = c("drought_positive", "wet_positive"), metadata = list())
```

## Arguments

- series:

  Aligned list of at least two `awi_series` objects in the hypothesized
  propagation order.

- roles:

  Unique labels for the nodes.

- orientation:

  Per-node convention: `"wet_positive"` or `"drought_positive"`. A
  scalar is recycled.

- target_orientation:

  Common direction to which all series are harmonized.

- metadata:

  Additional chain-level provenance.

## Value

An `awi_drought_chain` containing raw and sign-harmonized series,
ordered roles, orientation provenance, and the multiplication applied to
each node.

## Details

The raw series are retained. Harmonization only multiplies values by
plus or minus one to establish a common direction. It does not
standardize, detrend, filter, or infer a causal pathway.

## References

Long J, Xu C, Wang H, Li Z, Xu F (2026). Reconstruction of drought
propagation pathways: A global analysis of multitype propagation chains
and nonlinear mechanisms. Global and Planetary Change 256:105144.
doi:10.1016/j.gloplacha.2025.105144.
