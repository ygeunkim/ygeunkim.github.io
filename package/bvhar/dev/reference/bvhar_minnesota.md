# Fitting Bayesian VHAR of Minnesota Prior

This function fits BVHAR with Minnesota prior.

## Usage

``` r
bvhar_minnesota(
  y,
  har = c(5, 22),
  num_chains = 1,
  num_iter = 1000,
  num_burn = floor(num_iter/2),
  thinning = 1,
  bayes_spec = set_bvhar(),
  scale_variance = 0.05,
  include_mean = TRUE,
  verbose = FALSE,
  num_thread = 1
)

# S3 method for class 'bvharmn'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvharhm'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvharmn'
logLik(object, ...)

# S3 method for class 'bvharmn'
AIC(object, ...)

# S3 method for class 'bvharmn'
BIC(object, ...)

is.bvharmn(x)

# S3 method for class 'bvharmn'
knit_print(x, ...)

# S3 method for class 'bvharhm'
knit_print(x, ...)
```

## Arguments

- y:

  Time series data of which columns indicate the variables

- har:

  Numeric vector for weekly and monthly order. By default, `c(5, 22)`.

- num_chains:

  Number of MCMC chains

- num_iter:

  MCMC iteration number

- num_burn:

  Number of burn-in (warm-up). Half of the iteration is the default
  choice.

- thinning:

  Thinning every thinning-th iteration

- bayes_spec:

  A BVHAR model specification by [`set_bvhar()`](set_bvar.md) (default)
  or [`set_weight_bvhar()`](set_bvar.md).

- scale_variance:

  Proposal distribution scaling constant to adjust an acceptance rate

- include_mean:

  Add constant term (Default: `TRUE`) or not (`FALSE`)

- verbose:

  Print the progress bar in the console. By default, `FALSE`.

- num_thread:

  Number of threads

- x:

  Any object

- digits:

  digit option to print

- ...:

  not used

- object:

  A `bvharmn` object

## Value

`bvhar_minnesota()` returns an object `bvharmn`
[class](https://rdrr.io/r/base/class.html). It is a list with the
following components:

- coefficients:

  Posterior Mean

- fitted.values:

  Fitted values

- residuals:

  Residuals

- mn_mean:

  Posterior mean matrix of Matrix Normal distribution

- mn_prec:

  Posterior precision matrix of Matrix Normal distribution

- iw_scale:

  Posterior scale matrix of posterior inverse-wishart distribution

- iw_shape:

  Posterior shape of inverse-Wishart distribution (\\\nu_0\\ - obs + 2).
  \\\nu_0\\: nrow(Dummy observation) - k

- df:

  Numer of Coefficients: 3m + 1 or 3m

- m:

  Dimension of the time series

- obs:

  Sample size used when training = `totobs` - 22

- prior_mean:

  Prior mean matrix of Matrix Normal distribution: \\M_0\\

- prior_precision:

  Prior precision matrix of Matrix Normal distribution:
  \\\Omega_0^{-1}\\

- prior_scale:

  Prior scale matrix of inverse-Wishart distribution: \\\Psi_0\\

- prior_shape:

  Prior shape of inverse-Wishart distribution: \\\nu_0\\

- y0:

  \\Y_0\\

- design:

  \\X_0\\

- p:

  3, this element exists to run the other functions

- week:

  Order for weekly term

- month:

  Order for monthly term

- totobs:

  Total number of the observation

- type:

  include constant term (`const`) or not (`none`)

- HARtrans:

  VHAR linear transformation matrix: \\C\_{HAR}\\

- y:

  Raw input (`matrix`)

- call:

  Matched call

- process:

  Process string in the `bayes_spec`: `BVHAR_MN_VAR` (BVHAR-S) or
  `BVHAR_MN_VHAR` (BVHAR-L)

- spec:

  Model specification (`bvharspec`)

It is also `normaliw` and `bvharmod` class.

## Details

Apply Minnesota prior to Vector HAR: \\\Phi\\ (VHAR matrices) and
\\\Sigma_e\\ (residual covariance).

\$\$\Phi \mid \Sigma_e \sim MN(M_0, \Omega_0, \Sigma_e)\$\$ \$\$\Sigma_e
\sim IW(\Psi_0, \nu_0)\$\$ (MN: [matrix
normal](https://en.wikipedia.org/wiki/Matrix_normal_distribution), IW:
[inverse-wishart](https://en.wikipedia.org/wiki/Inverse-Wishart_distribution))

There are two types of Minnesota priors for BVHAR:

- VAR-type Minnesota prior specified by [`set_bvhar()`](set_bvar.md),
  so-called BVHAR-S model.

- VHAR-type Minnesota prior specified by
  [`set_weight_bvhar()`](set_bvar.md), so-called BVHAR-L model.

## References

Kim, Y. G., and Baek, C. (2024). *Bayesian vector heterogeneous
autoregressive modeling*. Journal of Statistical Computation and
Simulation, 94(6), 1139-1157.

## See also

- [`set_bvhar()`](set_bvar.md) to specify the hyperparameters of BVHAR-S

- [`set_weight_bvhar()`](set_bvar.md) to specify the hyperparameters of
  BVHAR-L

- [`summary.normaliw()`](summary.normaliw.md) to summarize BVHAR model

## Examples

``` r
# Perform the function using etf_vix dataset
fit <- bvhar_minnesota(y = etf_vix[,1:3])
class(fit)
#> [1] "bvharmn"  "bvharmod" "normaliw"

# Extract coef, fitted values, and residuals
coef(fit)
#>                       GVZCLS       OVXCLS      VXFXICLS
#> GVZCLS_day      4.054532e+00 0.0025388541  -0.042525189
#> OVXCLS_day     -1.366340e-02 0.8079110507  -0.031033473
#> VXFXICLS_day   -4.183438e-02 0.0051049793   4.054350108
#> GVZCLS_week    -2.492472e-02 0.0006387236  -0.010174733
#> OVXCLS_week    -2.833401e-03 0.0043564040  -0.007443461
#> VXFXICLS_week  -9.905209e-03 0.0012987538  -0.025290631
#> GVZCLS_month   -9.131393e-03 0.0003326060  -0.003836385
#> OVXCLS_month   -5.441029e-04 0.0018552343  -0.002963922
#> VXFXICLS_month -3.814722e-03 0.0005445523  -0.008484952
#> const          -5.310108e+01 5.0609176021 -72.647534276
head(residuals(fit))
#>           [,1]      [,2]       [,3]
#> [1,] -6.209055 0.5973066 -15.606244
#> [2,] -7.863082 0.7815828 -14.640328
#> [3,] -5.526879 1.0981949  -8.401639
#> [4,] -4.834885 1.2276147 -22.046621
#> [5,] -2.043235 1.1944296 -17.227425
#> [6,] -2.705064 2.1299231 -15.753897
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 26.89906 31.72269 45.06624
#> [2,] 27.89308 31.64842 43.31033
#> [3,] 25.25688 31.73181 40.13164
#> [4,] 23.90489 32.07239 52.51662
#> [5,] 21.28323 32.44557 47.41742
#> [6,] 21.98506 32.72008 46.26390
```
