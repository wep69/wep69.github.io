# Query DuckDB/DBI connection

Query DuckDB/DBI connection

## Usage

``` r
pheno_db_query(con, sql)
```

## Arguments

- con:

  DBI connection.

- sql:

  SQL query.

## Value

Tibble.

## Examples

``` r
if (FALSE) { # \dontrun{
  con<-pheno_db()
  pheno_db_query(con,"SELECT 1 AS x")
  DBI::dbDisconnect(con,shutdown=TRUE)
} # }
if (FALSE) pheno_db_query(con,"SELECT COUNT(*) AS n FROM traits") # \dontrun{}
if (FALSE) pheno_db_query(con,"SELECT * FROM traits LIMIT 5") # \dontrun{}
```
