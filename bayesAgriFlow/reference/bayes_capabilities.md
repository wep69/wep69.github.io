# Show the bayesAgriFlow capability registry

The registry is explicit about which design structures are available
through each engine. A backend is selected because it supports the
requested scientific structure, not simply because it is installed.

## Usage

``` r
bayes_capabilities(design = NULL)
```

## Arguments

- design:

  Optional design type used to filter the registry.

## Value

A data frame.

## Examples

``` r
# Example 1: all capabilities
bayes_capabilities()
#>                design      engine             status estimation evidence
#> 1                 crd      native          supported       TRUE    FALSE
#> 2                 crd bayesfactor supported_optional      FALSE     TRUE
#> 3                 crd        brms supported_optional       TRUE     TRUE
#> 4                 crd        pymc      not_supported      FALSE    FALSE
#> 5                rcbd      native          supported       TRUE    FALSE
#> 6                rcbd bayesfactor supported_optional      FALSE     TRUE
#> 7                rcbd        brms supported_optional       TRUE     TRUE
#> 8                rcbd        pymc      not_supported      FALSE    FALSE
#> 9        latin_square      native          supported       TRUE    FALSE
#> 10       latin_square bayesfactor supported_optional      FALSE     TRUE
#> 11       latin_square        brms supported_optional       TRUE     TRUE
#> 12       latin_square        pymc      not_supported      FALSE    FALSE
#> 13          factorial      native          supported       TRUE    FALSE
#> 14          factorial bayesfactor supported_optional      FALSE     TRUE
#> 15          factorial        brms supported_optional       TRUE     TRUE
#> 16          factorial        pymc      not_supported      FALSE    FALSE
#> 17         split_plot      native      not_supported      FALSE    FALSE
#> 18         split_plot bayesfactor      not_supported      FALSE    FALSE
#> 19         split_plot        brms supported_optional       TRUE     TRUE
#> 20         split_plot        pymc      not_supported      FALSE    FALSE
#> 21         regression      native          supported       TRUE    FALSE
#> 22         regression bayesfactor      not_supported      FALSE    FALSE
#> 23         regression        brms supported_optional       TRUE     TRUE
#> 24         regression        pymc      not_supported      FALSE    FALSE
#> 25         polynomial      native          supported       TRUE    FALSE
#> 26         polynomial bayesfactor      not_supported      FALSE    FALSE
#> 27         polynomial        brms supported_optional       TRUE     TRUE
#> 28         polynomial        pymc      not_supported      FALSE    FALSE
#> 29             ancova      native          supported       TRUE    FALSE
#> 30             ancova bayesfactor      not_supported      FALSE    FALSE
#> 31             ancova        brms supported_optional       TRUE     TRUE
#> 32             ancova        pymc      not_supported      FALSE    FALSE
#> 33         qual_quant      native          supported       TRUE    FALSE
#> 34         qual_quant bayesfactor      not_supported      FALSE    FALSE
#> 35         qual_quant        brms supported_optional       TRUE     TRUE
#> 36         qual_quant        pymc      not_supported      FALSE    FALSE
#> 37              mixed      native      not_supported      FALSE    FALSE
#> 38              mixed bayesfactor      not_supported      FALSE    FALSE
#> 39              mixed        brms supported_optional       TRUE     TRUE
#> 40              mixed        pymc      not_supported      FALSE    FALSE
#> 41           repeated      native      not_supported      FALSE    FALSE
#> 42           repeated bayesfactor      not_supported      FALSE    FALSE
#> 43           repeated        brms supported_optional       TRUE     TRUE
#> 44           repeated        pymc      not_supported      FALSE    FALSE
#> 45        split_split      native      not_supported      FALSE    FALSE
#> 46        split_split bayesfactor      not_supported      FALSE    FALSE
#> 47        split_split        brms supported_optional       TRUE     TRUE
#> 48        split_split        pymc      not_supported      FALSE    FALSE
#> 49         strip_plot      native      not_supported      FALSE    FALSE
#> 50         strip_plot bayesfactor      not_supported      FALSE    FALSE
#> 51         strip_plot        brms supported_optional       TRUE     TRUE
#> 52         strip_plot        pymc      not_supported      FALSE    FALSE
#> 53               glmm      native      not_supported      FALSE    FALSE
#> 54               glmm bayesfactor      not_supported      FALSE    FALSE
#> 55               glmm        brms supported_optional       TRUE     TRUE
#> 56               glmm        pymc      not_supported      FALSE    FALSE
#> 57          nonlinear      native      not_supported      FALSE    FALSE
#> 58          nonlinear bayesfactor      not_supported      FALSE    FALSE
#> 59          nonlinear        brms supported_optional       TRUE     TRUE
#> 60          nonlinear        pymc      not_supported      FALSE    FALSE
#> 61             smooth      native      not_supported      FALSE    FALSE
#> 62             smooth bayesfactor      not_supported      FALSE    FALSE
#> 63             smooth        brms supported_optional       TRUE     TRUE
#> 64             smooth        pymc      not_supported      FALSE    FALSE
#> 65                 gp      native      not_supported      FALSE    FALSE
#> 66                 gp bayesfactor      not_supported      FALSE    FALSE
#> 67                 gp        brms supported_optional       TRUE     TRUE
#> 68                 gp        pymc      not_supported      FALSE    FALSE
#> 69               bart      native      not_supported      FALSE    FALSE
#> 70               bart bayesfactor      not_supported      FALSE    FALSE
#> 71               bart        brms      not_supported      FALSE    FALSE
#> 72               bart        pymc supported_optional       TRUE    FALSE
#> 73            spatial      native      not_supported      FALSE    FALSE
#> 74            spatial bayesfactor      not_supported      FALSE    FALSE
#> 75            spatial        brms supported_optional       TRUE     TRUE
#> 76            spatial        pymc      not_supported      FALSE    FALSE
#> 77                gxe      native      not_supported      FALSE    FALSE
#> 78                gxe bayesfactor      not_supported      FALSE    FALSE
#> 79                gxe        brms supported_optional       TRUE     TRUE
#> 80                gxe        pymc      not_supported      FALSE    FALSE
#> 81       multivariate      native      not_supported      FALSE    FALSE
#> 82       multivariate bayesfactor      not_supported      FALSE    FALSE
#> 83       multivariate        brms supported_optional       TRUE     TRUE
#> 84       multivariate        pymc      not_supported      FALSE    FALSE
#> 85  measurement_error      native      not_supported      FALSE    FALSE
#> 86  measurement_error bayesfactor      not_supported      FALSE    FALSE
#> 87  measurement_error        brms supported_optional       TRUE     TRUE
#> 88  measurement_error        pymc      not_supported      FALSE    FALSE
#> 89            missing      native      not_supported      FALSE    FALSE
#> 90            missing bayesfactor      not_supported      FALSE    FALSE
#> 91            missing        brms supported_optional       TRUE     TRUE
#> 92            missing        pymc      not_supported      FALSE    FALSE
#> 93           censored      native      not_supported      FALSE    FALSE
#> 94           censored bayesfactor      not_supported      FALSE    FALSE
#> 95           censored        brms supported_optional       TRUE     TRUE
#> 96           censored        pymc      not_supported      FALSE    FALSE
#> 97            ordinal      native      not_supported      FALSE    FALSE
#> 98            ordinal bayesfactor      not_supported      FALSE    FALSE
#> 99            ordinal        brms supported_optional       TRUE     TRUE
#> 100           ordinal        pymc      not_supported      FALSE    FALSE
#> 101           mixture      native      not_supported      FALSE    FALSE
#> 102           mixture bayesfactor      not_supported      FALSE    FALSE
#> 103           mixture        brms supported_optional       TRUE     TRUE
#> 104           mixture        pymc      not_supported      FALSE    FALSE
#> 105    distributional      native      not_supported      FALSE    FALSE
#> 106    distributional bayesfactor      not_supported      FALSE    FALSE
#> 107    distributional        brms supported_optional       TRUE     TRUE
#> 108    distributional        pymc      not_supported      FALSE    FALSE
#>     hierarchical non_gaussian flexible_response
#> 1          FALSE        FALSE             FALSE
#> 2          FALSE        FALSE             FALSE
#> 3          FALSE        FALSE             FALSE
#> 4          FALSE        FALSE             FALSE
#> 5          FALSE        FALSE             FALSE
#> 6           TRUE        FALSE             FALSE
#> 7           TRUE        FALSE             FALSE
#> 8          FALSE        FALSE             FALSE
#> 9          FALSE        FALSE             FALSE
#> 10          TRUE        FALSE             FALSE
#> 11         FALSE        FALSE             FALSE
#> 12         FALSE        FALSE             FALSE
#> 13         FALSE        FALSE             FALSE
#> 14          TRUE        FALSE             FALSE
#> 15          TRUE        FALSE             FALSE
#> 16         FALSE        FALSE             FALSE
#> 17         FALSE        FALSE             FALSE
#> 18         FALSE        FALSE             FALSE
#> 19          TRUE        FALSE             FALSE
#> 20         FALSE        FALSE             FALSE
#> 21         FALSE        FALSE             FALSE
#> 22         FALSE        FALSE             FALSE
#> 23         FALSE        FALSE             FALSE
#> 24         FALSE        FALSE             FALSE
#> 25         FALSE        FALSE             FALSE
#> 26         FALSE        FALSE             FALSE
#> 27         FALSE        FALSE             FALSE
#> 28         FALSE        FALSE             FALSE
#> 29         FALSE        FALSE             FALSE
#> 30         FALSE        FALSE             FALSE
#> 31          TRUE        FALSE             FALSE
#> 32         FALSE        FALSE             FALSE
#> 33         FALSE        FALSE             FALSE
#> 34         FALSE        FALSE             FALSE
#> 35          TRUE        FALSE             FALSE
#> 36         FALSE        FALSE             FALSE
#> 37         FALSE        FALSE             FALSE
#> 38         FALSE        FALSE             FALSE
#> 39          TRUE        FALSE             FALSE
#> 40         FALSE        FALSE             FALSE
#> 41         FALSE        FALSE             FALSE
#> 42         FALSE        FALSE             FALSE
#> 43          TRUE        FALSE             FALSE
#> 44         FALSE        FALSE             FALSE
#> 45         FALSE        FALSE             FALSE
#> 46         FALSE        FALSE             FALSE
#> 47          TRUE        FALSE             FALSE
#> 48         FALSE        FALSE             FALSE
#> 49         FALSE        FALSE             FALSE
#> 50         FALSE        FALSE             FALSE
#> 51          TRUE        FALSE             FALSE
#> 52         FALSE        FALSE             FALSE
#> 53         FALSE        FALSE             FALSE
#> 54         FALSE        FALSE             FALSE
#> 55          TRUE         TRUE             FALSE
#> 56         FALSE        FALSE             FALSE
#> 57         FALSE        FALSE             FALSE
#> 58         FALSE        FALSE             FALSE
#> 59         FALSE        FALSE              TRUE
#> 60         FALSE        FALSE             FALSE
#> 61         FALSE        FALSE             FALSE
#> 62         FALSE        FALSE             FALSE
#> 63         FALSE        FALSE              TRUE
#> 64         FALSE        FALSE             FALSE
#> 65         FALSE        FALSE             FALSE
#> 66         FALSE        FALSE             FALSE
#> 67         FALSE        FALSE              TRUE
#> 68         FALSE        FALSE             FALSE
#> 69         FALSE        FALSE             FALSE
#> 70         FALSE        FALSE             FALSE
#> 71         FALSE        FALSE              TRUE
#> 72         FALSE         TRUE              TRUE
#> 73         FALSE        FALSE             FALSE
#> 74         FALSE        FALSE             FALSE
#> 75          TRUE        FALSE              TRUE
#> 76         FALSE        FALSE             FALSE
#> 77         FALSE        FALSE             FALSE
#> 78         FALSE        FALSE             FALSE
#> 79          TRUE        FALSE             FALSE
#> 80         FALSE        FALSE             FALSE
#> 81         FALSE        FALSE             FALSE
#> 82         FALSE        FALSE             FALSE
#> 83          TRUE         TRUE             FALSE
#> 84         FALSE        FALSE             FALSE
#> 85         FALSE        FALSE             FALSE
#> 86         FALSE        FALSE             FALSE
#> 87          TRUE        FALSE             FALSE
#> 88         FALSE        FALSE             FALSE
#> 89         FALSE        FALSE             FALSE
#> 90         FALSE        FALSE             FALSE
#> 91          TRUE        FALSE             FALSE
#> 92         FALSE        FALSE             FALSE
#> 93         FALSE        FALSE             FALSE
#> 94         FALSE        FALSE             FALSE
#> 95          TRUE         TRUE             FALSE
#> 96         FALSE        FALSE             FALSE
#> 97         FALSE        FALSE             FALSE
#> 98         FALSE        FALSE             FALSE
#> 99          TRUE         TRUE             FALSE
#> 100        FALSE        FALSE             FALSE
#> 101        FALSE        FALSE             FALSE
#> 102        FALSE        FALSE             FALSE
#> 103         TRUE         TRUE              TRUE
#> 104        FALSE        FALSE             FALSE
#> 105        FALSE        FALSE             FALSE
#> 106        FALSE        FALSE             FALSE
#> 107         TRUE         TRUE              TRUE
#> 108        FALSE        FALSE             FALSE
#>                                                                                      notes
#> 1                                                      Conjugate Gaussian teaching engine.
#> 2                                             Default-prior Gaussian model-space evidence.
#> 3                                Stan posterior model through brms with cmdstanr or rstan.
#> 4              PyMC is not a general replacement backend for this design in version 1.0.0.
#> 5                                                      Conjugate Gaussian teaching engine.
#> 6                                             Default-prior Gaussian model-space evidence.
#> 7                                Stan posterior model through brms with cmdstanr or rstan.
#> 8              PyMC is not a general replacement backend for this design in version 1.0.0.
#> 9                                                      Conjugate Gaussian teaching engine.
#> 10                                            Default-prior Gaussian model-space evidence.
#> 11                               Stan posterior model through brms with cmdstanr or rstan.
#> 12             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 13                                                     Conjugate Gaussian teaching engine.
#> 14                                            Default-prior Gaussian model-space evidence.
#> 15                               Stan posterior model through brms with cmdstanr or rstan.
#> 16             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 17                                    The native engine does not represent this structure.
#> 18                                       No validated BayesFactor adapter for this design.
#> 19                               Stan posterior model through brms with cmdstanr or rstan.
#> 20             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 21                                                     Conjugate Gaussian teaching engine.
#> 22                                       No validated BayesFactor adapter for this design.
#> 23                               Stan posterior model through brms with cmdstanr or rstan.
#> 24             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 25                                                     Conjugate Gaussian teaching engine.
#> 26                                       No validated BayesFactor adapter for this design.
#> 27                               Stan posterior model through brms with cmdstanr or rstan.
#> 28             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 29                                                     Conjugate Gaussian teaching engine.
#> 30                                       No validated BayesFactor adapter for this design.
#> 31                               Stan posterior model through brms with cmdstanr or rstan.
#> 32             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 33                                                     Conjugate Gaussian teaching engine.
#> 34                                       No validated BayesFactor adapter for this design.
#> 35                               Stan posterior model through brms with cmdstanr or rstan.
#> 36             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 37                                    The native engine does not represent this structure.
#> 38                                       No validated BayesFactor adapter for this design.
#> 39                               Stan posterior model through brms with cmdstanr or rstan.
#> 40             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 41                                    The native engine does not represent this structure.
#> 42                                       No validated BayesFactor adapter for this design.
#> 43                               Stan posterior model through brms with cmdstanr or rstan.
#> 44             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 45                                    The native engine does not represent this structure.
#> 46                                       No validated BayesFactor adapter for this design.
#> 47                               Stan posterior model through brms with cmdstanr or rstan.
#> 48             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 49                                    The native engine does not represent this structure.
#> 50                                       No validated BayesFactor adapter for this design.
#> 51                               Stan posterior model through brms with cmdstanr or rstan.
#> 52             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 53                                    The native engine does not represent this structure.
#> 54                                       No validated BayesFactor adapter for this design.
#> 55                               Stan posterior model through brms with cmdstanr or rstan.
#> 56             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 57                                    The native engine does not represent this structure.
#> 58                                       No validated BayesFactor adapter for this design.
#> 59                               Stan posterior model through brms with cmdstanr or rstan.
#> 60             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 61                                    The native engine does not represent this structure.
#> 62                                       No validated BayesFactor adapter for this design.
#> 63                               Stan posterior model through brms with cmdstanr or rstan.
#> 64             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 65                                    The native engine does not represent this structure.
#> 66                                       No validated BayesFactor adapter for this design.
#> 67                               Stan posterior model through brms with cmdstanr or rstan.
#> 68             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 69                                    The native engine does not represent this structure.
#> 70                                       No validated BayesFactor adapter for this design.
#> 71                                   Not routed through brms; use optional PyMC/PyMC-BART.
#> 72                                                       PyMC-BART sum-of-trees posterior.
#> 73                                    The native engine does not represent this structure.
#> 74                                       No validated BayesFactor adapter for this design.
#> 75              Two-dimensional Gaussian-process field adjustment plus optional hierarchy.
#> 76             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 77                                    The native engine does not represent this structure.
#> 78                                       No validated BayesFactor adapter for this design.
#> 79  Explicit genotype, environment, interaction, and environment-specific block hierarchy.
#> 80             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 81                                    The native engine does not represent this structure.
#> 82                                       No validated BayesFactor adapter for this design.
#> 83      Joint responses with response-specific families and optional residual correlation.
#> 84             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 85                                    The native engine does not represent this structure.
#> 86                                       No validated BayesFactor adapter for this design.
#> 87                    Known predictor error through brms mi() latent-variable formulation.
#> 88             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 89                                    The native engine does not represent this structure.
#> 90                                       No validated BayesFactor adapter for this design.
#> 91                   One-step continuous missing-predictor/response model using brms mi().
#> 92             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 93                                    The native engine does not represent this structure.
#> 94                                       No validated BayesFactor adapter for this design.
#> 95                     Left, right, or interval censoring through response addition terms.
#> 96             PyMC is not a general replacement backend for this design in version 1.0.0.
#> 97                                    The native engine does not represent this structure.
#> 98                                       No validated BayesFactor adapter for this design.
#> 99           Cumulative, continuation-ratio, stopping-ratio, and adjacent-category models.
#> 100            PyMC is not a general replacement backend for this design in version 1.0.0.
#> 101                                   The native engine does not represent this structure.
#> 102                                      No validated BayesFactor adapter for this design.
#> 103                         Finite mixture likelihood with user-specified component count.
#> 104            PyMC is not a general replacement backend for this design in version 1.0.0.
#> 105                                   The native engine does not represent this structure.
#> 106                                      No validated BayesFactor adapter for this design.
#> 107          Predictors for scale, shape, precision, or related distributional parameters.
#> 108            PyMC is not a general replacement backend for this design in version 1.0.0.
# Example 2: repeated-measures capabilities
bayes_capabilities("repeated")
#>     design      engine             status estimation evidence hierarchical
#> 1 repeated      native      not_supported      FALSE    FALSE        FALSE
#> 2 repeated bayesfactor      not_supported      FALSE    FALSE        FALSE
#> 3 repeated        brms supported_optional       TRUE     TRUE         TRUE
#> 4 repeated        pymc      not_supported      FALSE    FALSE        FALSE
#>   non_gaussian flexible_response
#> 1        FALSE             FALSE
#> 2        FALSE             FALSE
#> 3        FALSE             FALSE
#> 4        FALSE             FALSE
#>                                                                         notes
#> 1                        The native engine does not represent this structure.
#> 2                           No validated BayesFactor adapter for this design.
#> 3                   Stan posterior model through brms with cmdstanr or rstan.
#> 4 PyMC is not a general replacement backend for this design in version 1.0.0.
# Example 3: generalized mixed-model route
subset(bayes_capabilities(), design == "glmm")
#>    design      engine             status estimation evidence hierarchical
#> 53   glmm      native      not_supported      FALSE    FALSE        FALSE
#> 54   glmm bayesfactor      not_supported      FALSE    FALSE        FALSE
#> 55   glmm        brms supported_optional       TRUE     TRUE         TRUE
#> 56   glmm        pymc      not_supported      FALSE    FALSE        FALSE
#>    non_gaussian flexible_response
#> 53        FALSE             FALSE
#> 54        FALSE             FALSE
#> 55         TRUE             FALSE
#> 56        FALSE             FALSE
#>                                                                          notes
#> 53                        The native engine does not represent this structure.
#> 54                           No validated BayesFactor adapter for this design.
#> 55                   Stan posterior model through brms with cmdstanr or rstan.
#> 56 PyMC is not a general replacement backend for this design in version 1.0.0.
```
