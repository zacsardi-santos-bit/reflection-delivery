Implement methods to access elements in list and struct columns within Daft expressions. Enable retrieval of list elements by index and struct fields by name, with error handling for invalid operations.

*   Update `ExpressionListNamespace` in `daft/expressions/expressions.py`:
    *   Implement the `get` method with the signature `get(self, idx: int | Expression, default: object = None) -> Expression`.
    *   Accept an integer or an `Expression` as the index argument.
    *   Return the element at the specified index for each row in a list column.
        *   Support both positive and negative indices.
        *   Use the `default` value if the index is out of bounds or the list row is null; return null if no default is provided.
        *   Return null if the element at the valid index is null, regardless of the default.
    *   Ensure indices are in bounds for fixed-size list columns, treating indices with absolute value >= N as out of bounds.
    *   Raise a `ValueError` if `get` is called on a non-list column.

*   Update `ExpressionStructNamespace` in `daft/expressions/expressions.py`:
    *   Implement the `get` method with the signature `get(self, name: str) -> Expression`.
    *   Retrieve the named field from a struct column, returning an `Expression` with the field's name and type.
    *   Return null for null struct rows.
    *   Raise a `ValueError` if the field name does not exist in the struct schema or if the column is not a struct type.

*   Update the `Expression` class in `daft/expressions/expressions.py`:
    *   Implement the `struct` property to return an `ExpressionStructNamespace` instance.
    *   Enable struct-specific operations using the `.struct` accessor, such as `col('x').struct.get('field')`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.