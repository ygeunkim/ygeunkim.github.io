# Fitting Bayesian VAR(p) of Minnesota Prior

This function fits BVAR(p) with Minnesota prior.

## Usage

``` r
bvar_minnesota(
  y,
  p = 1,
  num_chains = 1,
  num_iter = 1000,
  num_burn = floor(num_iter/2),
  thinning = 1,
  bayes_spec = set_bvar(),
  scale_variance = 0.05,
  include_mean = TRUE,
  verbose = FALSE,
  num_thread = 1
)

# S3 method for class 'bvarmn'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvarhm'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvarmn'
logLik(object, ...)

# S3 method for class 'bvarmn'
AIC(object, ...)

# S3 method for class 'bvarmn'
BIC(object, ...)

is.bvarmn(x)

# S3 method for class 'bvarmn'
knit_print(x, ...)

# S3 method for class 'bvarhm'
knit_print(x, ...)
```

## Arguments

- y:

  Time series data of which columns indicate the variables

- p:

  VAR lag (Default: 1)

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

  A BVAR model specification by [`set_bvar()`](set_bvar.md).

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

  A `bvarmn` object

## Value

`bvar_minnesota()` returns an object `bvarmn`
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

  Posterior scale matrix of posterior inverse-Wishart distribution

- iw_shape:

  Posterior shape of inverse-Wishart distribution (\\alpha_0\\ - obs +
  2). \\\alpha_0\\: nrow(Dummy observation) - k

- df:

  Numer of Coefficients: mp + 1 or mp

- m:

  Dimension of the time series

- obs:

  Sample size used when training = `totobs` - `p`

- prior_mean:

  Prior mean matrix of Matrix Normal distribution: \\A_0\\

- prior_precision:

  Prior precision matrix of Matrix Normal distribution:
  \\\Omega_0^{-1}\\

- prior_scale:

  Prior scale matrix of inverse-Wishart distribution: \\S_0\\

- prior_shape:

  Prior shape of inverse-Wishart distribution: \\\alpha_0\\

- y0:

  \\Y_0\\

- design:

  \\X_0\\

- p:

  Lag of VAR

- totobs:

  Total number of the observation

- type:

  include constant term (`const`) or not (`none`)

- y:

  Raw input (`matrix`)

- call:

  Matched call

- process:

  Process string in the `bayes_spec`: `BVAR_Minnesota`

- spec:

  Model specification (`bvharspec`)

It is also `normaliw` and `bvharmod` class.

## Details

Minnesota prior gives prior to parameters \\A\\ (VAR matrices) and
\\\Sigma_e\\ (residual covariance).

\$\$A \mid \Sigma_e \sim MN(A_0, \Omega_0, \Sigma_e)\$\$ \$\$\Sigma_e
\sim IW(S_0, \alpha_0)\$\$ (MN: [matrix
normal](https://en.wikipedia.org/wiki/Matrix_normal_distribution), IW:
[inverse-wishart](https://en.wikipedia.org/wiki/Inverse-Wishart_distribution))

## References

Bańbura, M., Giannone, D., & Reichlin, L. (2010). *Large Bayesian vector
auto regressions*. Journal of Applied Econometrics, 25(1).

Giannone, D., Lenza, M., & Primiceri, G. E. (2015). *Prior Selection for
Vector Autoregressions*. Review of Economics and Statistics, 97(2).

Litterman, R. B. (1986). *Forecasting with Bayesian Vector
Autoregressions: Five Years of Experience*. Journal of Business &
Economic Statistics, 4(1), 25.

KADIYALA, K.R. and KARLSSON, S. (1997), *NUMERICAL METHODS FOR
ESTIMATION AND INFERENCE IN BAYESIAN VAR-MODELS*. J. Appl. Econ., 12:
99-132.

Karlsson, S. (2013). *Chapter 15 Forecasting with Bayesian Vector
Autoregression*. Handbook of Economic Forecasting, 2, 791-897.

Sims, C. A., & Zha, T. (1998). *Bayesian Methods for Dynamic
Multivariate Models*. International Economic Review, 39(4), 949-968.

## See also

- [`set_bvar()`](set_bvar.md) to specify the hyperparameters of
  Minnesota prior.

- [`summary.normaliw()`](summary.normaliw.md) to summarize BVAR model

## Examples

``` r
# Perform the function using etf_vix dataset
fit <- bvar_minnesota(y = etf_vix[,1:3], p = 2)
class(fit)
#> [1] "bvarmn"   "bvharmod" "normaliw"

# Extract coef, fitted values, and residuals
coef(fit)
#>                   GVZCLS       OVXCLS      VXFXICLS
#> GVZCLS_1     3.023961404  0.008064301  -0.035100057
#> OVXCLS_1    -0.011961599  0.517451473  -0.025881986
#> VXFXICLS_1  -0.036349016  0.017709869   2.966076494
#> GVZCLS_2    -0.021721329  0.002050118  -0.008557404
#> OVXCLS_2    -0.002778245  0.014643962  -0.006376402
#> VXFXICLS_2  -0.008815944  0.004352893  -0.021942713
#> const      -35.026832526 12.651415043 -46.615007041
head(residuals(fit))
#>           [,1]      [,2]      [,3]
#> [1,] -5.370563 3.1347761 -7.263639
#> [2,] -8.626907 4.1750682 -8.371738
#> [3,] -6.778437 2.6643755 -5.499029
#> [4,] -5.359589 2.3138623 -9.189213
#> [5,] -5.791923 1.9321743 -8.334212
#> [6,] -6.884178 0.9987274 -7.781396
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 27.71056 32.38522 36.32364
#> [2,] 30.22691 32.41493 36.83174
#> [3,] 27.97844 32.95562 35.03903
#> [4,] 26.75959 32.48114 38.29421
#> [5,] 27.39192 32.03783 37.00421
#> [6,] 28.02418 31.59127 35.74140
```
