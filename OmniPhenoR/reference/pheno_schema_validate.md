# Validate a tabular schema

Validate a tabular schema

## Usage

``` r
pheno_schema_validate(data, schema, required = names(schema))
```

## Arguments

- data:

  Data frame.

- schema:

  Named list mapping columns to `character`, `numeric`, `integer`,
  `logical`, or `Date`.

- required:

  Required column names.

## Value

Diagnostic tibble.

## Examples

``` r
pheno_schema_validate(data.frame(id="a",value=1),
  list(id="character",value="numeric"),c("id","value"))
#> # A tibble: 2 × 6
#>   field required present expected  actual    valid
#>   <chr> <lgl>    <lgl>   <chr>     <chr>     <lgl>
#> 1 id    TRUE     TRUE    character character TRUE 
#> 2 value TRUE     TRUE    numeric   numeric   TRUE 
pheno_schema_validate(data.frame(id="a"),list(id="character",value="numeric"),"value")
#> # A tibble: 2 × 6
#>   field required present expected  actual    valid
#>   <chr> <lgl>    <lgl>   <chr>     <chr>     <lgl>
#> 1 id    FALSE    TRUE    character character TRUE 
#> 2 value TRUE     FALSE   numeric   NA        FALSE
pheno_schema_validate(data.frame(day=as.Date("2026-01-01")),list(day="Date"))
#> # A tibble: 1 × 6
#>   field required present expected actual valid
#>   <chr> <lgl>    <lgl>   <chr>    <chr>  <lgl>
#> 1 day   TRUE     TRUE    Date     Date   TRUE 
```
