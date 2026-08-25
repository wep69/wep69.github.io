# Define a contributor

Define a contributor

## Usage

``` r
pheno_person(
  person_id,
  name,
  orcid = NA_character_,
  role = NA_character_,
  affiliation = NA_character_
)
```

## Arguments

- person_id:

  Identifier.

- name:

  Name.

- orcid:

  Optional ORCID.

- role:

  Role.

- affiliation:

  Affiliation.

## Value

One-row data frame.

## Examples

``` r
pheno_person("p1","Researcher A",role="investigator")
#>   person_id         name orcid         role affiliation
#> 1        p1 Researcher A  <NA> investigator        <NA>
pheno_person("p2","Researcher B",orcid="0000-0000-0000-0000",role="analyst")
#>   person_id         name               orcid    role affiliation
#> 1        p2 Researcher B 0000-0000-0000-0000 analyst        <NA>
pheno_person("p3","Technician C",role="data_collector",affiliation="University")
#>   person_id         name orcid           role affiliation
#> 1        p3 Technician C  <NA> data_collector  University
```
