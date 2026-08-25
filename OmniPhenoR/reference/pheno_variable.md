# Define an observed phenotyping variable

Define an observed phenotyping variable

## Usage

``` r
pheno_variable(
  variable_id,
  trait,
  method,
  scale,
  unit,
  ontology = NA_character_,
  accession = NA_character_
)
```

## Arguments

- variable_id:

  Stable variable id.

- trait:

  Biological trait concept.

- method:

  Measurement/computation method.

- scale:

  Scale representation.

- unit:

  Unit label.

- ontology:

  Optional ontology name.

- accession:

  Optional ontology accession/URI.

## Value

One-row `pheno_variable` data frame.

## Examples

``` r
pheno_variable("cc_exg","green canopy cover","ExG + Otsu","proportion","1")
#>   variable_id              trait     method      scale unit ontology accession
#> 1      cc_exg green canopy cover ExG + Otsu proportion    1     <NA>      <NA>
pheno_variable("height","plant height","ruler","continuous","cm")
#>   variable_id        trait method      scale unit ontology accession
#> 1      height plant height  ruler continuous   cm     <NA>      <NA>
pheno_variable("sev","disease severity","segmented lesion area","proportion","1",
  "Crop Ontology","CO_0000000")
#>   variable_id            trait                method      scale unit
#> 1         sev disease severity segmented lesion area proportion    1
#>        ontology  accession
#> 1 Crop Ontology CO_0000000
```
