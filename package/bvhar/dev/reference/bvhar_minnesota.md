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
#> GVZCLS_day      1.5236613461 6.843633e-04 -1.476293e-02
#> OVXCLS_day     -0.0027953087 9.762112e-01 -5.998437e-03
#> VXFXICLS_day   -0.0143359454 2.370681e-04  1.524300e+00
#> GVZCLS_week    -0.0090482072 1.677521e-04 -3.557504e-03
#> OVXCLS_week    -0.0005892590 4.329118e-04 -1.455029e-03
#> VXFXICLS_week  -0.0033642481 1.801698e-04 -9.049456e-03
#> GVZCLS_month   -0.0032372382 1.100300e-04 -1.308296e-03
#> OVXCLS_month   -0.0001114521 1.980318e-04 -5.662509e-04
#> VXFXICLS_month -0.0012961537 7.944401e-05 -3.019550e-03
#> const          -8.7416502773 6.267428e-01 -1.210906e+01
head(residuals(fit))
#>            [,1]          [,2]      [,3]
#> [1,] -0.7921447 -0.0004231276 -2.983436
#> [2,] -1.8249242  0.1973798969 -3.113070
#> [3,] -1.1333918  0.4906518013  1.142142
#> [4,] -1.2908943  0.5693860360 -4.776914
#> [5,] -0.1351714  0.4512025504 -3.142112
#> [6,] -0.3593605  1.3291893623 -2.390797
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 21.48214 32.32042 32.44344
#> [2,] 21.85492 32.23262 31.78307
#> [3,] 20.86339 32.33935 30.58786
#> [4,] 20.36089 32.73061 35.24691
#> [5,] 19.37517 33.18880 33.33211
#> [6,] 19.63936 33.52081 32.90080
```
