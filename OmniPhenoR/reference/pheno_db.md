# Open an optional DuckDB connection

Open an optional DuckDB connection

## Usage

``` r
pheno_db(path = ":memory:", read_only = FALSE)
```

## Arguments

- path:

  Database path or `:memory:`.

- read_only:

  Open read-only.

## Value

DBI connection.

## Examples

``` r
if (FALSE) con <- pheno_db(":memory:"); DBI::dbDisconnect(con, shutdown=TRUE) # \dontrun{}
#> Error in h(simpleError(msg, call)): error in evaluating the argument 'conn' in selecting a method for function 'dbDisconnect': object 'con' not found
if (FALSE) con <- pheno_db(tempfile(fileext=".duckdb")) # \dontrun{}
if (FALSE) pheno_db(":memory:",read_only=FALSE) # \dontrun{}
```
