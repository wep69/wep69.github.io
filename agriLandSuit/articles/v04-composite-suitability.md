# Composite suitability and limiting factors

## Purpose

Version 0.4.0 introduces composition without erasing criterion-level
information. Three aggregation rules are available: limiting factor,
weighted arithmetic, and weighted geometric.

## Why weights are separate from crop profiles

A crop requirement represents agronomic evidence about response to
temperature, soil depth, pH, slope, or another environmental variable. A
weight represents a choice about how multiple criteria are integrated.
These are different scientific objects and are stored separately.

## Aggregation

``` r

criteria <- crop_criteria(land, crop)

strict <- suit_aggregate(criteria, method = "limiting")
compensatory <- suit_aggregate(
  criteria,
  method = "weighted_arithmetic",
  weights = c(temp = .30, rain = .30, depth = .25, slope = .15)
)
partial <- suit_aggregate(
  criteria,
  method = "weighted_geometric",
  weights = c(temp = .30, rain = .30, depth = .25, slope = .15)
)
```

`limiting` is non-compensatory. Weighted arithmetic allows complete
compensation according to the selected weights. Weighted geometric
penalizes imbalanced criteria more strongly and retains exact zero as
zero.

## Missing information

The default `na_policy = "propagate"` leaves a composite unknown when a
required criterion is unknown. `na_policy = "available"` is an explicit
alternative that recalculates the local weighted result over available
criteria. It should be justified scientifically rather than used as
implicit imputation.

## Domain summaries

``` r

d <- domain_suitability(criteria, method = "limiting")
summary(d)
```

This creates climate, soil, terrain, and water composites only for
represented domains while preserving all individual criterion scores in
the original object.

## Limiting factor diagnostics

``` r

lf <- limiting_factor(criteria)
summary(lf)
```

The diagnostic stores the minimum score, a criterion index, and the
number of tied limiting criteria. Ties are retained as information
rather than silently discarded.

## Explicit restrictions and final classes

``` r

effects <- constraint_effects(constraint_evaluate(land, rules))
final <- land_suitability(criteria, method = "limiting", constraints = effects)
plot(final, type = "score")
plot(final, type = "class")
```

Restrictions are applied after aggregation. The unconstrained composite
remains stored for auditing. Excluded land remains missing by default
rather than being recoded to zero suitability.
