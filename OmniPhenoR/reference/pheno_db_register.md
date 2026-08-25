# Register an R table with DuckDB

Register an R table with DuckDB

## Usage

``` r
pheno_db_register(con, name, data, overwrite = TRUE)
```

## Arguments

- con:

  DBI connection.

- name:

  Table name.

- data:

  Data frame.

- overwrite:

  Overwrite existing table.

## Value

`TRUE` invisibly.

## Examples

``` r
if (FALSE) con<-pheno_db();pheno_db_register(con,"traits",data.frame(x=1:3));D
#> Error in h(simpleError(msg, call)): error in evaluating the argument 'conn' in selecting a method for function 'dbWriteTable': object 'con' not found
    BI::dbDisconnect(con,shutdown=TRUE) # \dontrun{}
#> Error in loadNamespace(x): there is no package called 'BI'
if (FALSE) pheno_db_register(con,"traits",data.frame(x=1:3),overwrite=TRUE) # \dontrun{}
if (FALSE) pheno_db_query(con,"SELECT * FROM traits") # \dontrun{}
```
