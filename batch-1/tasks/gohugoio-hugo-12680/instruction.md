Enhance Hugo's math template namespace by implementing trigonometric functions and angle conversion utilities. Add methods to access the mathematical constant pi, compute trigonometric values, and convert between degrees and radians.

*   Implement the `Pi()` method in `tpl/math/math.go`:
    *   Return the mathematical constant pi as a float64.
    *   Ensure it takes no arguments and returns no error.

*   Implement trigonometric functions in `tpl/math/math.go`:
    *   `Sin(n any) (float64, error)`: Return the sine of a radian argument.
    *   `Cos(n any) (float64, error)`: Return the cosine of a radian argument.
    *   `Tan(n any) (float64, error)`: Return the tangent of a radian argument.
        *   Return NaN for positive infinity without error.
    *   Ensure all functions accept any numeric type and return an error for non-numeric inputs.

*   Implement inverse trigonometric functions in `tpl/math/math.go`:
    *   `Asin(n any) (float64, error)`: Return the arcsine in radians.
    *   `Acos(n any) (float64, error)`: Return the arccosine in radians.
    *   `Atan(n any) (float64, error)`: Return the arctangent in radians.
        *   Return approximately π/2 for positive infinity.
    *   `Atan2(x any, y any) (float64, error)`: Return the arc tangent of x/y, considering quadrant.
    *   Ensure all functions accept any numeric type and return an error for non-numeric inputs.
    *   Return NaN for out-of-domain inputs without error.

*   Implement angle conversion utilities in `tpl/math/math.go`:
    *   `ToDegrees(n any) (float64, error)`: Convert radians to degrees.
        *   Ensure ToDegrees(π/2) equals 90.0 and ToDegrees(π) equals 180.0.
    *   `ToRadians(n any) (float64, error)`: Convert degrees to radians.
        *   Ensure ToRadians(90) equals approximately π/2 and ToRadians(180) equals approximately π.
    *   Ensure both functions accept any numeric type and return an error for non-numeric inputs.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.