# Specify an experimental design or hierarchical model structure

Creates an explicit design object. Experimental roles, response family,
grouping structure, residual structure, and computationally relevant
model options are stored separately from the statistical backend. This
separation lets the same scientific design be routed to different
validated engines as capabilities are added in later releases.

## Usage

``` r
bayes_design(data, response, treatment = NULL, block = NULL,
                         row = NULL, column = NULL, factors = NULL,
                         whole_plot = NULL, subplot = NULL,
                         whole_plot_id = NULL, subsubplot = NULL,
                         subplot_id = NULL, strip_row = NULL,
                         strip_column = NULL, strip_row_id = NULL,
                         strip_column_id = NULL, quantitative = NULL,
                         covariate = NULL, fixed = NULL, group = NULL,
                         random_slopes = NULL, nested = NULL, crossed = NULL,
                         subject = NULL, time = NULL, residual_group = NULL,
                         correlation = c("none", "ar1"), ar_order = 1L,
                         trials = NULL, exposure = NULL,
                         family = c("gaussian", "student", "bernoulli",
                                    "binomial", "poisson", "negbinomial",
                                    "gamma", "lognormal", "beta"),
                         link = NULL, degree = 2L,
                         ancova_interaction = FALSE,
                         type = c("crd", "rcbd", "latin_square",
                                  "factorial", "split_plot", "regression",
                                  "polynomial", "ancova", "qual_quant",
                                  "mixed", "repeated", "split_split",
                                  "strip_plot", "glmm", "nonlinear",
                                  "smooth", "gp", "bart"),
                         future_roles = list())
```

## Arguments

- data:

  A data frame.

- response:

  Name of the response column.

- treatment:

  Name of the treatment factor for CRD, RCBD, Latin square, ANCOVA, or
  qualitative-by-quantitative models.

- block:

  Name of the block factor when applicable.

- row:

  Name of the Latin-square row factor.

- column:

  Name of the Latin-square column factor.

- factors:

  Character vector of qualitative factorial predictors.

- whole_plot:

  Name of the whole-plot treatment factor.

- subplot:

  Name of the subplot treatment factor.

- whole_plot_id:

  Globally unique whole-plot experimental-unit identifier.

- subsubplot:

  Name of the sub-subplot treatment factor for split-split plots.

- subplot_id:

  Globally unique subplot experimental-unit identifier for split-split
  plots.

- strip_row:

  Name of the treatment randomized to row-oriented strips.

- strip_column:

  Name of the treatment randomized to column-oriented strips.

- strip_row_id:

  Identifier for row-oriented strip experimental units.

- strip_column_id:

  Identifier for column-oriented strip experimental units.

- quantitative:

  Name of a quantitative predictor for regression models.

- covariate:

  Name of a quantitative covariate for ANCOVA.

- fixed:

  Fixed-effect specification for mixed, repeated, and GLMM models.
  Supply a one-sided formula or a character vector of column names.

- group:

  Character vector of random-intercept grouping factors.

- random_slopes:

  Named list mapping grouping factors to random-slope predictors, for
  example \`list(block = "dose")\`.

- nested:

  Optional list of nested grouping chains such as \`list(c("site",
  "block"))\`.

- crossed:

  Optional character vector of crossed random-intercept factors.

- subject:

  Subject or experimental-unit identifier for repeated measures.

- time:

  Time variable for repeated measures.

- residual_group:

  Optional factor used to model heterogeneous residual standard
  deviations in Gaussian or Student-t models.

- correlation:

  Residual correlation structure. Version 0.3.0 supports \`"none"\` and
  \`"ar1"\` for repeated-measures models.

- ar_order:

  Autoregressive order. Version 0.3.0 uses order 1 for the
  repeated-measures convenience API.

- trials:

  Binomial trials column.

- exposure:

  Optional positive exposure column used as a log offset for Poisson and
  negative-binomial models.

- family:

  Response family. Supported values are \`"gaussian"\`, \`"student"\`,
  \`"bernoulli"\`, \`"binomial"\`, \`"poisson"\`, \`"negbinomial"\`,
  \`"gamma"\`, \`"lognormal"\`, and \`"beta"\`.

- link:

  Optional link override stored for the brms family constructor.

- degree:

  Polynomial degree (1 to 3) for polynomial designs.

- ancova_interaction:

  Logical; include treatment-by-covariate interaction.

- type:

  Design/model type.

- future_roles:

  Optional named list of recognized future design roles. They are stored
  but not silently used by version 0.3.0.

## Value

An object of class \`bayes_design\`.

## Examples

``` r
# Example 1: completely randomized design
d1 <- bayes_teaching_data("crd")
bayes_design(d1, "yield", treatment = "treatment", type = "crd")
#> <bayes_design>
#>   Type: Completely randomized design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   treatment: treatment

# Example 2: repeated measures with a subject-specific time slope
d2 <- bayes_teaching_data("repeated")
bayes_design(d2, "height", fixed = ~ treatment * time,
             subject = "plot_id", time = "time",
             group = "plot_id", random_slopes = list(plot_id = "time"),
             type = "repeated")
#> <bayes_design>
#>   Type: Repeated-measures mixed model
#>   Response: height
#>   Family: Gaussian
#>   Observations: 120
#>   fixed: treatment, time
#>   group: plot_id
#>   subject: plot_id
#>   time: time
#>   random_slopes: plot_id:[time]

# Example 3: negative-binomial mixed model
d3 <- bayes_teaching_data("glmm_count")
bayes_design(d3, "insects", fixed = ~ treatment, group = "block",
             family = "negbinomial", type = "glmm")
#> <bayes_design>
#>   Type: Generalized linear mixed model
#>   Response: insects
#>   Family: Negative binomial
#>   Observations: 72
#>   fixed: treatment
#>   group: block
```
