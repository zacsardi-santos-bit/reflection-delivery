I'm working on implementing a class method on Python's built-in complex type that converts a single numeric value to a complex number.

*   The complex type must expose a class method named `from_number` that accepts exactly one positional numeric argument and raises TypeError when called with no arguments or with non-numeric arguments.

*   When `from_number` is called on the base complex class with an argument that is already an instance of the exact complex type (not a subclass), it must return the same object (identity preserved, not a copy).

*   When `from_number` receives a float, an int, or a complex value, it must return a complex number with the corresponding value and with type equal to the class the method was called on (cls).

*   When `from_number` receives an instance of a complex subclass, it must extract the complex value and return a new instance whose type is exactly cls (not the subclass of the input).

*   When `from_number` receives an object that implements the `__complex__` protocol, it must call that protocol and return the resulting complex value as an instance of cls.

*   When `from_number` receives an object that implements the `__float__` protocol (but not `__complex__`), it must call that protocol and return the result as a complex instance of cls.

*   When `from_number` receives an object that implements the `__index__` protocol (but not `__complex__` or `__float__`), it must call that protocol and return the result as a complex instance of cls.

*   When `from_number` receives a string, bytes object, dict, or an object that implements only `__int__` (without `__index__`), it must raise TypeError.

*   When `from_number` is called on a subclass of complex (e.g. ComplexSubclass), the returned value must be an instance of that subclass, not of the base complex type.


*   Interface details: Type: Method (class method)
Name: from_number
Location: crates/vm/src/builtins/complex.rs (exposed as complex.from_number in Python)
Signature: complex.from_number(cls, x) -> complex
Description: A class method on the built-in complex type. Accepts a single numeric argument x and returns a complex number whose type is exactly cls (the class the method was called on). When cls is the base complex class and x is already an exact complex instance, returns x itself (identity). Raises TypeError for non-numeric inputs including strings, bytes, dicts, and objects with only __int__ (not __index__). Accepted input types: float, int, complex, complex subclass instances, and objects with __complex__, __float__, or __index__ protocols.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.