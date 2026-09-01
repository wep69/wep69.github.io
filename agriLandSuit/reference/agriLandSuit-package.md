# agriLandSuit: Agricultural Land Suitability and Edaphoclimatic Zoning

Version 1.0.0 consolidates the complete scientific workflow for spatial
agricultural land suitability and edaphoclimatic zoning, from explicit
crop requirements and fuzzy criterion scoring through constraints,
composite suitability, MCDA, climate scenarios, uncertainty propagation,
multi-crop comparison, and reproducibility infrastructure.

## Details

The package is R-first. Native R and terra are the authoritative
computational path for spatial analysis. Python is optional through
reticulate for selected fuzzy, MCDA, and numerical sampling tasks and
never changes the public R API. Crop requirements, decision weights,
land constraints, scenario metadata, and uncertainty assumptions are
represented separately so that each analytical choice remains auditable.
Climate-risk products may be consumed through an explicit adapter but
are not recomputed or automatically inverted into suitability.
