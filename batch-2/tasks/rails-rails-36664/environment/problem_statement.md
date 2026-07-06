## Description

Numericality validation with an "equal to" constraint incorrectly rejects records when the attribute is a decimal column with a defined precision. The validator compares the raw input value against the expected value, but it does not account for how the database will actually store the value after applying the column's precision constraints.

## Example of the Problem

Suppose a decimal column is defined to store numbers with 2 decimal places. If you validate that the column must equal 10,000,000.12 and then create a record with the value 10,000,000.121, the database would store 10,000,000.12 (rounded to the column's precision) — which matches the expected value. However, the validator currently rejects the record because it compares the raw 10,000,000.121 against 10,000,000.12 without considering the column's precision.

## Expected Behavior

- When validating equality on a decimal column with a defined precision, the comparison should use the value as it would actually be stored (i.e., after casting to the column's type), not the raw input.
- When validating equality on a decimal column without an explicit precision, or on a virtual decimal attribute, floating-point representation differences at very high precision should not cause false validation failures.

## Why This Matters

Developers using numericality equality validations on precision-constrained decimal columns are forced to manually pre-round their comparison values or input values to avoid spurious failures. The validation should naturally account for the column's precision, making equality checks work as intuitively expected.
