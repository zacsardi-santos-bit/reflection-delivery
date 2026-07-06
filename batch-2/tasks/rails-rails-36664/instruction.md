Implement a fix for numericality validation with an 'equal_to' constraint on decimal columns with defined precision. Ensure that the validation compares the stored value, after applying the column's precision, rather than the raw input value.

*   Ensure that when a numericality validation with an 'equal_to' constraint is applied to a decimal column with defined precision:
    *   The comparison is made against the value as stored in the database, considering the column's precision and scale.
    *   A raw input value differing only in digits beyond the column's precision is treated as equal and valid.
*   Ensure that when a numericality validation with an 'equal_to' constraint is applied to a decimal column without explicit precision:
    *   Values differing from the expected value only due to floating-point representation at high precision are considered valid.
*   Ensure that when a numericality validation with an 'equal_to' constraint is applied to a virtual attribute declared with the decimal type:
    *   Values differing from the expected value only due to floating-point representation at high precision are considered valid.
*   Update the 'numeric_data' test table in the schema to include a 'decimal_number' column of type decimal without explicit precision or scale.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.