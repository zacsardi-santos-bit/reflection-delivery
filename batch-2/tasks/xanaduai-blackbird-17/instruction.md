Implement a conversion function to transform 2D Python lists into Blackbird script array declarations, update the program serializer to handle Python lists, and ensure arithmetic operations on array variables yield correct results.

*   Implement `list_to_blackbird` in `blackbird_python/blackbird/program.py`:
    *   Accept a 2D Python nested list and a variable name string.
    *   Return a list of strings representing a Blackbird array declaration.
    *   Detect and format elements as integers, floats, or complex numbers.
    *   Raise `ValueError` with 'unsupported type' for unsupported element types.

*   Update `BlackbirdProgram` in `blackbird_python/blackbird/program.py`:
    *   Ensure `serialize()` raises `ValueError` with 'Unknown argument type' when positional or keyword arguments contain NumPy arrays instead of Python lists.
    *   Use `list_to_blackbird` to serialize operations with 2D Python lists as array arguments.

*   Modify `_expression` in `blackbird_python/blackbird/auxiliary.py`:
    *   Handle subtraction, division, and exponentiation for array variables stored as Python lists.
    *   Ensure operations produce element-wise results:
        *   Subtraction: array variable - scalar.
        *   Division: array variable / scalar.
        *   Exponentiation: scalar ** array variable, array variable ** scalar, array variable ** array variable.
    *   Ensure array variables in `_VAR` are Python lists and operations match NumPy results.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.