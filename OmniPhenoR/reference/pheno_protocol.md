# Define an experimental or processing protocol

Define an experimental or processing protocol

## Usage

``` r
pheno_protocol(
  protocol_id,
  name,
  version = NA_character_,
  uri = NA_character_,
  description = NA_character_
)
```

## Arguments

- protocol_id:

  Identifier.

- name:

  Protocol name.

- version:

  Version.

- uri:

  Optional persistent URI.

- description:

  Description.

## Value

One-row data frame.

## Examples

``` r
pheno_protocol("p1","RGB acquisition","1.0")
#>   protocol_id            name version  uri description
#> 1          p1 RGB acquisition     1.0 <NA>        <NA>
pheno_protocol("p2","Leaf segmentation","2.1",description="ExG threshold")
#>   protocol_id              name version  uri   description
#> 1          p2 Leaf segmentation     2.1 <NA> ExG threshold
pheno_protocol("p3","UAV mission","2026-01",uri="https://example.org/protocol")
#>   protocol_id        name version                          uri description
#> 1          p3 UAV mission 2026-01 https://example.org/protocol        <NA>
```
