# Prepare or execute a BrAPI request

Network access is opt-in. `dry_run=TRUE` is the default and is the mode
used in examples, tests, and vignettes.

## Usage

``` r
pheno_brapi_call(
  client,
  endpoint,
  method = c("GET", "POST", "PUT", "DELETE"),
  body = NULL,
  dry_run = TRUE
)
```

## Arguments

- client:

  `pheno_brapi`.

- endpoint:

  Endpoint path.

- method:

  HTTP verb.

- body:

  Optional request body.

- dry_run:

  Return request specification without network activity.

## Value

Request specification or parsed response.

## Examples

``` r
c<-pheno_brapi("https://example.org");pheno_brapi_call(c,"/brapi/v2/studies")
#> $url
#> [1] "https://example.org/brapi/v2/studies"
#> 
#> $method
#> [1] "GET"
#> 
#> $body
#> NULL
#> 
#> $version
#> [1] "2.1"
#> 
#> $authenticated
#> [1] FALSE
#> 
#> attr(,"class")
#> [1] "pheno_brapi_request"
pheno_brapi_call(c,"/brapi/v2/observations","POST",body=list(observationDbId="x"))
#> $url
#> [1] "https://example.org/brapi/v2/observations"
#> 
#> $method
#> [1] "POST"
#> 
#> $body
#> $body$observationDbId
#> [1] "x"
#> 
#> 
#> $version
#> [1] "2.1"
#> 
#> $authenticated
#> [1] FALSE
#> 
#> attr(,"class")
#> [1] "pheno_brapi_request"
if (FALSE) pheno_brapi_call(c,"/brapi/v2/studies",dry_run=FALSE) # \dontrun{}
```
