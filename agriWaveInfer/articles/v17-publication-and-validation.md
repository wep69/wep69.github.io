# Scientific validation and publication readiness

The 1.0.0 consolidated release separates source-level evidence from
runtime evidence. A static audit, frozen numerical references, metadata
reconciliation, and deterministic source archives may be completed
without claiming that `R CMD check`, backend differential tests, or
rendered vignettes have passed when no R runtime is available.

``` r

awi_publication_check(".", expected_version = "1.0.0")
```

Research-software metadata and provenance are treated as scientific
outputs, consistent with FAIR4RS principles (Barker et al. 2022).
Publication-specific requirements should be rechecked at submission time
because journal policies can change.

Barker, Michelle, Neil P. Chue Hong, Daniel S. Katz, et al. 2022.
“Introducing the FAIR Principles for Research Software.” *Scientific
Data* 9: 622. <https://doi.org/10.1038/s41597-022-01710-x>.
