# Agricultural (season) year of a date

Assigns each month to an agricultural year that starts in
\`start_month\`. With the default \`label = "end"\`, a July to June
season is labelled by the year of its January, so July 1991 to June 1992
is season 1992.

## Usage

``` r
agri_year(x, month = NULL, start_month = 7L, label = c("end", "start"))
```

## Arguments

- x:

  A \`Date\` vector, or an integer vector of calendar years when
  \`month\` is supplied.

- month:

  Optional integer months (1 to 12), used when \`x\` holds years.

- start_month:

  First month of the agricultural year.

- label:

  \`end\` labels the season by the calendar year in which it ends,
  \`start\` by the year in which it begins.

## Value

Integer vector of agricultural years.

## Examples

``` r
agri_year(as.Date(c("1991-06-15", "1991-07-15", "1992-01-15")))
#> [1] 1991 1992 1992
```
