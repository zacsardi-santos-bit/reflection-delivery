Implement fixes for several identified bugs in SciPy's ODE solver and statistics modules to ensure correct functionality and user notifications.

*   Update the `select_initial_step` function in `scipy/integrate/_ivp/common.py`:
    *   Ensure the initial step size respects the `max_step` parameter for both forward and backward integration across all solver orders.
    *   The returned step size must not exceed `max_step` when it is a finite value.

*   Modify the `solve_ivp` function to:
    *   Prevent evaluation of the ODE right-hand side function outside the integration interval `[t_span[0], t_span[1]]` for methods RK23, RK45, DOP853, Radau, and BDF.
    *   Handle zero-length intervals by returning a successful result with the final state equal to the initial condition.
    *   Ensure that with `max_step=1e-20`, the first call to `solver.step()` succeeds, and the second call fails with `solver.status` set to 'failed' and a message indicating 'step size is less'.

*   Implement deprecation warnings for the `stats.trapz` distribution alias:
    *   Issue a `DeprecationWarning` for each method (`pdf`, `logpdf`, `cdf`, `logcdf`, `sf`, `logsf`, `ppf`, `isf`) with the message pattern '`trapz.{method}` is deprecated'.
    *   Ensure each deprecated method returns the same numerical result as the corresponding `stats.trapezoid` method with identical arguments.

*   Adjust the `stats.bootstrap` function to:
    *   Accept multi-sample inputs with different sizes along the resampling axis without raising warnings.
    *   Issue a `FutureWarning` when sample arrays have incompatible shapes in non-axis dimensions, with a message matching 'Ignoring the dimension specified by `axis`...'.

*   Verify that `stats.ttest_1samp` operates correctly within the `axis_nan_policy` framework:
    *   Ensure functionality when `axis=None` is used with `nan_policy` set to 'omit' or 'propagate', including maintaining `keepdims` behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.