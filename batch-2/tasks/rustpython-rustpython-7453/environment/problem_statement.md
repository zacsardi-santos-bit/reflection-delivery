## Description

Python's complex type is missing a class method for converting a single numeric value to a complex number in a type-safe, subclass-friendly way. The standard constructor is too broad — it accepts strings, handles multiple argument forms, and doesn't guarantee the returned type matches the subclass it is called on. This makes it difficult to implement reliable numeric coercion in complex subclasses.

## Expected Behavior

- A new class method should be available on the complex type that accepts any single numeric value (float, integer, complex, or any object implementing a recognized numeric protocol) and returns a complex number.
- The method must always return an instance of the class it is called on — if called on a subclass of complex, the result must be an instance of that subclass.
- When no conversion is needed (the input is already an exact complex object and the method is called on the base complex class), the method should return the same object rather than creating a copy.
- Non-numeric types such as strings, bytes, and objects that only implement a plain integer conversion (not the index protocol) must be rejected with a type error.
- Objects that implement numeric protocols such as float conversion, index conversion, or complex conversion must be accepted.

## Why This Matters

Without this method, subclasses of complex have no clean way to coerce an arbitrary numeric value to their subclass type. The constructor accepts too much (including strings) and doesn't guarantee the return type. This method fills that gap, enabling consistent and type-safe numeric coercion in complex and its subclasses.
