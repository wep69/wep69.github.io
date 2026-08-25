# Define germplasm metadata

Define germplasm metadata

## Usage

``` r
pheno_germplasm(
  germplasm_id,
  name,
  species,
  accession = NA_character_,
  pedigree = NA_character_
)
```

## Arguments

- germplasm_id:

  Identifier.

- name:

  Germplasm/cultivar name.

- species:

  Scientific species name.

- accession:

  Optional accession.

- pedigree:

  Optional pedigree.

## Value

One-row data frame.

## Examples

``` r
pheno_germplasm("g1","BRS Jade","Gossypium hirsutum")
#>   germplasm_id     name            species accession pedigree
#> 1           g1 BRS Jade Gossypium hirsutum      <NA>     <NA>
pheno_germplasm("g2","Line 27","Zea mays",accession="ACC27")
#>   germplasm_id    name  species accession pedigree
#> 1           g2 Line 27 Zea mays     ACC27     <NA>
pheno_germplasm("g3","Cultivar A","Glycine max",pedigree="P1/P2")
#>   germplasm_id       name     species accession pedigree
#> 1           g3 Cultivar A Glycine max      <NA>    P1/P2
```
