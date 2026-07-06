## Description

Several bugs have been identified in SciPy's ODE solver and statistics modules that need to be fixed:

1. **ODE solver evaluates function outside integration bounds**: When solving ODEs, the solver sometimes calls the user's derivative function at time values beyond the integration endpoint. This causes failures when the function is only defined within the integration domain — any call outside the interval throws an error. Multiple solver methods are affected (regression for gh-17341, gh-8848, gh-9198).

2. **Initial step size ignores the maximum step constraint**: The function that computes the initial step size for ODE integration does not respect the user-specified maximum step size. When a maximum step size is provided, the first step can still exceed it, which is inconsistent with the behavior of subsequent steps.

3. **Missing deprecation warnings on deprecated distribution alias**: A distribution in the statistics module was deprecated in favor of a newer name. However, accessing the statistical methods on the old alias (such as the density function, cumulative distribution, survival function, and quantile functions) does not currently issue any deprecation warning. Users should be warned each time they use a deprecated method so they can update their code.

4. **Bootstrap resampling behaves incorrectly for multi-dimensional samples**: When calling the bootstrap function with two samples of different sizes along the resampling axis — which is expected behavior for two-sample test statistics — the function should succeed without any warning. Conversely, when sample shapes are incompatible in non-axis dimensions, the function should issue a warning to alert users of potential issues but currently may not do so consistently (regression for gh-20850).

## Expected Behavior

- The ODE solver must never evaluate the derivative function outside the integration interval.
- The initial step size selection must honor the user-specified maximum step size constraint.
- All methods on the deprecated distribution alias must issue a deprecation warning with a clear message identifying which method is deprecated.
- Bootstrap must accept two-sample inputs where sizes differ along the resampling axis without warnings, and must warn when shapes are incompatible in other dimensions.

## Why This Matters

These bugs can cause unexpected crashes or silent incorrect results, especially when ODE functions are only defined on a restricted domain, when maximum step sizes are critical for numerical accuracy, or when users unknowingly rely on a deprecated interface.
