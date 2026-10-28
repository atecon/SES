# Simple exponential smoothing (SES)

Simple exponential smoothing (ETS(A,N,N)) for gretl, following Hyndman and Athanasopoulos (Sec. 8.1, 2021) (https://otexts.com/fpp3/ses.html).

The level evolves as

    l_t = alpha x y_t + (1 - alpha) x l_{t-1}

The one-step forecast of `y_t` is `l_{t-1}`, and all multi-step forecasts are
flat at the last level. The smoothing parameter `alpha` (restricted to [0,1])
and the initial level `l_0` are estimated jointly by minimizing the sum of
squared one-step forecast errors (BFGS with box constraints on `alpha`).

Requires time-series data and gretl 2024c or later.

## Related packages

Two further user-contributed gretl packages cover related methods:

- `Holt`: Holt's linear trend method (double exponential smoothing), which extends SES with a trend component.

- `Winters`: Holt-Winters seasonal smoothing, which adds a seasonal component.

- `ema_tools`: `ema_alpha_opt()` finds the optimal smoothing parameter `alpha` for SES by minimizing the sum of squared one-step forecast errors. The result can be passed to gretl's `movavg()`, which applies SES for a given `alpha`.




## Graphical interface

Available under *Variable > Filter > SES*. With parameters set to `automatic`, `alpha` is estimated; with `pre-specified` the given `alpha` is used. The initial level is always estimated. `ntest` and `verbose` as above.


## Public functions

### `bundle ses(series y, bundle specs[null])`

Estimates the model and returns a bundle. Optional keys of `specs`:

- `alpha`: Fixed smoothing parameter in [0,1]; if absent, it is estimated (default: none).
- `init`: Fixed initial level `l_0`; if absent, it is estimated (default: none).
- `ntest`: Number of trailing observations held back to test the forecasts (default: 0).
- `verbose`: Verbosity, 0 to 3 (default: 1).

The training range is the contiguous non-missing range of `y`, minus the last
`ntest` observations. If `ntest` is 0 and the sample contains observations
beyond the end of `y` (e.g. added by `dataset addobs`), flat out-of-sample
forecasts are computed for them. Missing values inside `y` are an error.

Returned bundle:

- `alpha`: Smoothing parameter.
- `l0`: Initial level.
- `theta`: Vector (alpha, l0).
- `SSE`: Training sum of squared one-step errors.
- `RMSE_train`: Training root mean squared error.
- `MAE_train`: Training mean absolute error.
- `RMSE_test`, `MAE_test`: Test statistics, only if `ntest > 0`.
- `H`: Matrix with columns `y`, `level`, `forecast` and, if there are forecasts beyond the training range, `stderr`.
- `ntest`: Number of test observations.
- `yname`: Name of the series.
- `limits`: Vector (start of training, end of training, end of forecasts).

In `H`, `forecast` is the one-step-ahead forecast inside the training range and
the flat multi-step forecast beyond it. `stderr` follows Hyndman and Athanasopoulos (2021, Sec. 8.7):

`sigma x sqrt(1 + (h-1) x alpha^2)`, with `sigma^2 = RMSE_train^2`.

With gretl 2026b or later the bundle also holds the virtual series `level`,
`fcast` and `irreg`, e.g. `series fc = b.fcast`.

### `void ses_plot_forecast(bundle b, matrix ci_levels[null])`

Plots actual and forecast values, with confidence bands if forecasts beyond the
training range exist. `ci_levels` holds two confidence levels between 50 and 99
(default `{80, 95}`).

### `void ses_plot_components(bundle b)`

Plots the level and the irregular component (one-step forecast errors).

### `matrix ses_ci_matrix(bundle b, matrix ci_levels[null])`

Returns a matrix with the lower and upper bounds (`Lo80`, `Hi80`, `Lo95`,
`Hi95`) of the forecast intervals; requires forecasts beyond the training range.

## Reference

Hyndman, R.J., and Athanasopoulos, G. (2021): Forecasting: Principles and
Practice, 3rd edition, OTexts. https://otexts.com/fpp3/
