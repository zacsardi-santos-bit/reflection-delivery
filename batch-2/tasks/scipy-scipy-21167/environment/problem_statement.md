## Description

SciPy's interpolation module currently lacks support for rational function approximation via the Adaptive Antoulas-Anderson algorithm. This is a well-established numerical method that constructs near-optimal rational approximants from function samples in barycentric form, and it is widely used in scientific computing for tasks that polynomial-based interpolation cannot handle well — particularly near singularities.

## Expected Behavior

A new approximation class should be added to the interpolation module that:

- Accepts a set of sample points and corresponding function values and constructs a rational approximation of the function.
- Returns a callable object that can be evaluated at new points, including graceful handling of special floating-point values like NaN and infinity.
- Exposes the selected support points, support values, barycentric weights, and per-iteration error estimates as attributes of the returned object.
- Provides methods to compute the poles, residues, and roots (zeros) of the rational approximation.
- Validates its inputs: raises an error if the sample points and values have mismatched sizes, if either is not a 1-D array, or if the sample points contain non-finite values.
- Accepts tolerance and maximum-terms parameters to control convergence, and warns the user if convergence is not achieved within the allowed number of terms.
- Preserves the numerical precision of the input data: the returned approximation and all of its attributes should use the same floating-point type as the inputs.

## Why This Matters

Rational approximation is far more powerful than polynomial interpolation for functions with poles or near-singularities. The AAA algorithm is particularly robust because it is adaptive (it selects support points automatically), numerically stable (it uses barycentric weights), and it provides direct access to the analytic structure of the approximated function through its poles, residues, and roots. Having this available in SciPy directly saves users from depending on external tools for a fundamental numerical computing capability.
