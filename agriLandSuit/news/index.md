# Changelog

## agriLandSuit 1.0.0

### Consolidated Scientific Release

- Consolidated the complete 0.1.0-0.9.0 API without adding a new
  analytical method or removing any public function.
- Preserved all 82 public exports and all registered S3 contracts from
  0.9.0.
- Added a complete end-to-end vignette linking data preparation, crop
  profiles, fuzzy scoring, constraints, aggregation, scenarios,
  uncertainty, multi-crop comparison, and reproducibility.
- Added small synthetic agronomic demonstration datasets under
  `inst/extdata` for deterministic and scenario examples. These datasets
  are explicitly illustrative and are not agronomic recommendations.
- Updated package-level documentation, README, architecture, optional
  Python backend documentation, vignette organization, release
  checklist, and Windows validation instructions for the 1.0.0 state.
- Replaced the stale static validator inherited from development
  releases with a 1.0.0 validator that checks the complete 82-function
  API, S3 registrations, documentation aliases, release assets,
  duplicate namespace entries, syntax-balance heuristics, and release
  metadata.
- Added explicit 1.0.0 API compatibility and release-asset tests.
- Added `inst/CITATION` for the software release without inventing a
  DOI.
- Audited current external infrastructure used or suggested by the
  package: `terra`, `digest`, `reticulate`, `targets`, `geotargets`,
  scikit-fuzzy, and PyMCDM.
- Kept Python optional. Native R/`terra` remains authoritative for
  spatial computation.
- Prepared a final source snapshot and cryptographic freeze workflow.
  The supplied creator/maintainer email `walterufpb@yahoo.com.br` is
  recorded in `Authors@R`; executable local R validation remains
  required before formal submission.

### Scientific invariants retained at 1.0.0

- Crop requirements are distinct from decision weights.
- Criteria are distinct from restrictions.
- Exclusion is distinct from suitability zero.
- No hidden unit conversion, resampling, risk inversion, missing-value
  imputation, or uncertainty distribution is performed.
- Climate-risk calculation remains outside `agriLandSuit`; only explicit
  external products are consumed.
- Scenario and probabilistic members remain identifiable until the
  requested summary stage.
- Multi-crop ties are retained explicitly rather than resolved by input
  order.
- Provenance and fingerprints never modify scientific results.

## agriLandSuit 0.9.0

- Added reproducibility infrastructure with SHA-256 object fingerprints,
  provenance records, and run manifests.
- Added portable `bundle` and packed `rds` exchange through
  [`land_export()`](https://wep69.github.io/agriLandSuit/reference/land_export.md)
  and
  [`land_import()`](https://wep69.github.io/agriLandSuit/reference/land_import.md).
- Added explicit external contracts for raster, land-data, climate-risk,
  and suitability interoperability.
- Added
  [`reproducibility_check()`](https://wep69.github.io/agriLandSuit/reference/reproducibility_check.md)
  for object/input integrity diagnostics.
- Added lightweight Markdown reporting with
  [`suit_report()`](https://wep69.github.io/agriLandSuit/reference/suit_report.md).
- Added
  [`targets_template()`](https://wep69.github.io/agriLandSuit/reference/targets_template.md)
  with optional `geotargets` patterns for `terra` rasters.
- Added `digest` as a core dependency and `targets`/`geotargets` as
  optional workflow integrations.
- Preserved the complete 0.8.0 public API.

## agriLandSuit 0.8.0

### Multi-crop comparison, ranking, and decision stability

- Added
  [`compare_crops()`](https://wep69.github.io/agriLandSuit/reference/compare_crops.md)
  for aligned deterministic comparison of two or more crop suitability
  results.
- Added
  [`crop_rank()`](https://wep69.github.io/agriLandSuit/reference/crop_rank.md)
  with explicit tie and missing-value policies.
- Added
  [`best_crop()`](https://wep69.github.io/agriLandSuit/reference/best_crop.md)
  and
  [`second_best_crop()`](https://wep69.github.io/agriLandSuit/reference/second_best_crop.md)
  with auditable crop-code keys and conservative handling of ties.
- Added
  [`decision_margin()`](https://wep69.github.io/agriLandSuit/reference/decision_margin.md)
  to quantify the score separation between the two leading crops.
- Added
  [`crop_winner_probability()`](https://wep69.github.io/agriLandSuit/reference/crop_winner_probability.md)
  for the probability that each crop is best across aligned uncertainty
  ensembles.
- Added
  [`crop_rank_stability()`](https://wep69.github.io/agriLandSuit/reference/crop_rank_stability.md)
  with mean rank, rank SD, probability of being best, winner entropy,
  and winner-probability margin.
- Preserved all 66 public exports from 0.7.0.

### Scientific safeguards

- Multi-crop comparison never re-computes or harmonizes crop suitability
  silently; individual crop analyses must already be comparable in
  scenario, management, aggregation, constraints, and geometry.
- Deterministic top-score ties are retained explicitly. By default, a
  tied cell has no arbitrarily assigned winner code and receives
  decision margin zero.
- Probabilistic ties split ensemble probability mass equally among tied
  winners by default.
- Winner probabilities are computed member by member before
  summarization; ensemble means are not ranked as a substitute for
  probability of being best.
- Probabilistic crop comparison requires identical ensemble member IDs,
  order, and member weights across crops.
- No new Python dependency was added. Native R/`terra` remains
  authoritative because ranking and cell-wise winner calculations do not
  justify cross-language data transfer.

## agriLandSuit 0.7.0

### Uncertainty propagation and ensemble stability

- Added
  [`uncertainty_spec()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_spec.md)
  for explicit Beta score uncertainty and multiplicative lognormal
  weight uncertainty.
- Added
  [`suit_monte_carlo()`](https://wep69.github.io/agriLandSuit/reference/suit_monte_carlo.md)
  with reproducible R sampling and an optional NumPy backend for numeric
  workflows.
- Added
  [`ensemble_suitability()`](https://wep69.github.io/agriLandSuit/reference/ensemble_suitability.md)
  for aligned scenario/model suitability ensembles without averaging
  them prematurely.
- Added
  [`uncertainty_summary()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_summary.md)
  for weighted mean, SD, P05/P50/P95 (or user-selected quantiles), and
  available-member counts.
- Added
  [`class_probability()`](https://wep69.github.io/agriLandSuit/reference/class_probability.md)
  for P(N), P(S3), P(S2), and P(S1) under the configured class
  definition.
- Added
  [`class_stability()`](https://wep69.github.io/agriLandSuit/reference/class_stability.md)
  with modal class, maximum class probability, normalized entropy, and
  leading-class probability margin.
- Added
  [`uncertainty_decompose()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_decompose.md)
  for descriptive marginal eta-squared by declared ensemble source.
- Preserved all 59 public exports from 0.6.0.

### Scientific safeguards

- No uncertainty distribution is inferred automatically. Users must
  create an explicit
  [`uncertainty_spec()`](https://wep69.github.io/agriLandSuit/reference/uncertainty_spec.md).
- Beta perturbation preserves the \[0, 1\] support of suitability scores
  and preserves exact 0 and 1 boundaries.
- Weight perturbations are positive and re-normalized; under the
  `limiting` method, weight uncertainty is explicitly reported as having
  no effect.
- Scenario/model ensemble members are retained rather than silently
  averaged before uncertainty summaries are computed.
- Marginal eta-squared is descriptive and is not labeled as a Sobol
  index. Shares from correlated/non-orthogonal factors may overlap and
  must not be summed as an additive partition.
- Raster Monte Carlo remains native to R/`terra`; Python is optional and
  restricted to numeric sampling where NumPy adds a useful backend
  without spatial conversion overhead.
- `PyMC` and `ArviZ` remain optional future candidates and are not
  required by 0.7.0.

## agriLandSuit 0.6.0

### Climate scenarios and edaphoclimatic change

- Added
  [`land_scenario()`](https://wep69.github.io/agriLandSuit/reference/land_scenario.md)
  and
  [`scenario_set()`](https://wep69.github.io/agriLandSuit/reference/scenario_set.md)
  for explicit `scenario × model × period × management × pathway`
  metadata with one auditable baseline.
- Added
  [`scenario_suitability()`](https://wep69.github.io/agriLandSuit/reference/scenario_suitability.md)
  to run the same crop profile, aggregation rule, weights,
  classification, and optional constraints across aligned scenarios.
- Added
  [`scenario_delta()`](https://wep69.github.io/agriLandSuit/reference/scenario_delta.md)
  for absolute or relative score changes against the baseline.
- Added
  [`scenario_transition()`](https://wep69.github.io/agriLandSuit/reference/scenario_transition.md)
  for spatial class-transition coding using a stable transition key.
- Added
  [`scenario_area_change()`](https://wep69.github.io/agriLandSuit/reference/scenario_area_change.md)
  for transition counts or area summaries in ha, km2, or m2 using
  [`terra::cellSize()`](https://rspatial.github.io/terra/reference/cellSize.html).
- Added
  [`climate_risk_adapter()`](https://wep69.github.io/agriLandSuit/reference/climate_risk_adapter.md)
  as a dependency-free interoperability contract for precomputed
  `agriClimateRisk`/external climate-risk layers. The adapter never
  recalculates risk and never automatically converts risk into
  suitability.
- Preserved all 52 public exports from 0.5.0.

### Scientific safeguards

- Scenario geometry must be explicitly aligned before comparison. No
  hidden resampling occurs inside scenario analysis.
- One baseline scenario is required by default, preventing ambiguous
  delta and transition calculations.
- Crop requirements and MCDA weights remain unchanged across scenarios
  unless the user explicitly creates a different analysis.
- Risk layers imported from external tools are treated as precomputed
  inputs. Any transformation from risk to suitability must remain
  explicit in the crop criterion or constraint definition.
- Scenario comparison is deterministic in 0.6.0. Probabilistic
  ensembles, Monte Carlo propagation, class probabilities, and
  uncertainty decomposition remain reserved for 0.7.0.
- No new mandatory Python dependency is introduced. `xarray`,
  `rioxarray`, and `dask` remain optional interoperability/scaling
  candidates rather than hidden requirements.

## agriLandSuit 0.4.0

### Composite suitability and classification

- Added
  [`suit_weights()`](https://wep69.github.io/agriLandSuit/reference/suit_weights.md)
  with strict name matching, non-negative weights, and explicit
  normalization.
- Added
  [`suit_aggregate()`](https://wep69.github.io/agriLandSuit/reference/suit_aggregate.md)
  with `limiting`, `weighted_arithmetic`, and `weighted_geometric`
  aggregation.
- Added
  [`domain_suitability()`](https://wep69.github.io/agriLandSuit/reference/domain_suitability.md)
  to create one auditable composite per climate, soil, terrain, or water
  domain.
- Added
  [`limiting_factor()`](https://wep69.github.io/agriLandSuit/reference/limiting_factor.md)
  with minimum score, first limiting criterion, and tie-count
  diagnostics.
- Added
  [`suit_classify()`](https://wep69.github.io/agriLandSuit/reference/suit_classify.md)
  for explicit, user-controlled suitability classes.
- Added
  [`land_suitability()`](https://wep69.github.io/agriLandSuit/reference/land_suitability.md)
  as the first high-level composition workflow, preserving unconstrained
  scores and applying `agri_constraint_effects` only after aggregation.
- Added
  [`suitability_score()`](https://wep69.github.io/agriLandSuit/reference/suitability_score.md)
  and
  [`suitability_class()`](https://wep69.github.io/agriLandSuit/reference/suitability_class.md)
  accessors.
- Added S3 print, summary, data-frame, and plot methods for composite
  suitability objects.
- Added frozen numeric aggregation reference cases and raster/numeric
  regression tests.

### Scientific safeguards

- Crop requirements remain free of decision weights. Weighting is a
  separate aggregation-layer concept so that AHP/TOPSIS in 0.5.0 can
  supply weights without mutating agronomic profiles.
- `limiting` remains the non-compensatory option and is the recommended
  baseline for strict agronomic suitability.
- Weighted geometric aggregation restores exact zero suitability after
  logarithmic computation rather than replacing zero with an arbitrary
  small positive score.
- Suitability inputs are validated against \[0, 1\] and are never
  silently clipped.
- Missing values propagate by default. The alternative `available`
  policy must be explicitly selected and renormalizes weights only over
  observed criteria.
- Constraints preserve their 0.3.0 semantics: exclusion is distinct from
  suitability zero, caps limit the composite score, and penalties are
  multiplicative.
- No new Python dependency is introduced. Native R/`terra` is the
  authoritative 0.4.0 aggregation backend.

## agriLandSuit 0.3.0

### Soil, terrain, water, and explicit restrictions

- Added
  [`domain_criteria()`](https://wep69.github.io/agriLandSuit/reference/domain_criteria.md)
  plus
  [`soil_criteria()`](https://wep69.github.io/agriLandSuit/reference/soil_criteria.md),
  [`terrain_criteria()`](https://wep69.github.io/agriLandSuit/reference/terrain_criteria.md),
  and
  [`water_criteria()`](https://wep69.github.io/agriLandSuit/reference/water_criteria.md)
  to score one environmental domain without cross-criterion aggregation.
- Added
  [`land_constraint()`](https://wep69.github.io/agriLandSuit/reference/land_constraint.md)
  and
  [`constraint_set()`](https://wep69.github.io/agriLandSuit/reference/constraint_set.md)
  for explicit, auditable land-use rules.
- Added three distinct constraint semantics: `exclude`, `cap`, and
  `penalty`.
- Added threshold operators `gt`, `ge`, `lt`, `le`, `eq`, `ne`,
  `between`, `outside`, `in`, and `not_in`.
- Added
  [`constraint_evaluate()`](https://wep69.github.io/agriLandSuit/reference/constraint_evaluate.md)
  to resolve rule sources against `agri_land_data`, including optional
  strict unit checks.
- Added
  [`constraint_effects()`](https://wep69.github.io/agriLandSuit/reference/constraint_effects.md)
  to combine rules while preserving semantics: exclusions use logical
  union, caps use the most restrictive cap, and penalties multiply.
- Added
  [`apply_constraints()`](https://wep69.github.io/agriLandSuit/reference/apply_constraints.md)
  for applying explicit restrictions to a single criterion-suitability
  layer without yet aggregating criteria.
- Added
  [`constraint_summary()`](https://wep69.github.io/agriLandSuit/reference/constraint_summary.md)
  plus S3 print, summary, data-frame, and plot methods.
- Added deterministic reference cases and tests for domain filtering,
  rule validation, source resolution, unit mismatches, combined effects,
  and constrained scores.
- Corrected the native Gaussian membership formula inherited from 0.2.0
  to match `scikit-fuzzy::gaussmf` exactly:
  `exp(-((x-center)/sigma)^2)`. This is a numerical bug fix, not an API
  change.

### Scientific safeguards

- Excluded land is represented separately from agronomically unsuitable
  land. By default, applying exclusions produces `NA`, not suitability
  zero.
- Missing constraint-source values propagate as unknown rather than
  being silently treated as unconstrained.
- Version 0.3.0 still does not construct a final composite suitability
  index. Limiting-factor and weighted aggregation remain reserved for
  0.4.0.
- Python remains optional. The 0.3.0 constraint engine is implemented
  natively with `terra`; no Python dependency is added for functionality
  already handled reliably by R.

## agriLandSuit 0.2.0

### Criterion engine and fuzzy suitability

- Added fuzzy membership functions and an auditable criterion engine
  with optional `reticulate`/`scikit-fuzzy` parity.

## agriLandSuit 0.1.0

### Foundations

- Added `agri_land_data`, crop requirement/profile classes, geometry and
  unit validation, alignment/cropping, and optional backend discovery.

## agriLandSuit 0.5.0

### MCDA and decision support

- Added
  [`ahp_matrix()`](https://wep69.github.io/agriLandSuit/reference/ahp_matrix.md),
  [`ahp_weights()`](https://wep69.github.io/agriLandSuit/reference/ahp_weights.md),
  and
  [`ahp_consistency()`](https://wep69.github.io/agriLandSuit/reference/ahp_consistency.md)
  for auditable pairwise weighting with consistency diagnostics.
- Added
  [`expert_weights()`](https://wep69.github.io/agriLandSuit/reference/expert_weights.md)
  for aggregation of expert priority vectors or pairwise judgments.
- Added
  [`topsis_score()`](https://wep69.github.io/agriLandSuit/reference/topsis_score.md)
  and
  [`topsis_rank()`](https://wep69.github.io/agriLandSuit/reference/topsis_rank.md)
  for ranking alternatives from criterion suitability scores.
- Added
  [`weight_sensitivity()`](https://wep69.github.io/agriLandSuit/reference/weight_sensitivity.md)
  for deterministic one-at-a-time perturbation of criterion weights.
- Preserved all 45 public exports from 0.4.0.

### Optional Python backend

- `pymcdm` is now an active optional MCDA backend via `reticulate` for
  AHP and benefit-oriented TOPSIS matrix workflows.
- R remains the default and mandatory implementation; Python is never
  installed automatically.
- The Python TOPSIS backend is explicitly configured for vector
  normalization to match the native R implementation.
- `ahpy` remains an optional candidate for future hierarchical AHP
  workflows rather than a mandatory dependency.
