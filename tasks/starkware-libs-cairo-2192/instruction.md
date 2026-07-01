Revise the array element access operations to use immutable snapshots instead of requiring ownership transfer. Ensure that the returned element is a read-only reference and update the relevant parts of the Cairo language's array library and associated code to reflect these changes.

*   Modify the array element access libfunc (array_get<T>):
    *   Accept a snapshot of the array (Snapshot<Array<T>>) as the second parameter.
    *   On success, return (RangeCheck, @T) without the array.
    *   On failure, return (RangeCheck) without the array.
  
*   Update the Cairo standard library (corelib/array.cairo):
    *   Change the extern function array_get parameter from 'ref arr: Array::<T>' to 'arr: @Array::<T>'.
    *   Change its return type from 'Option::<T>' to 'Option::<@T>'.

*   Modify the ArrayTrait methods:
    *   Change 'get' method to take 'self: @Array::<T>' and return 'Option::<@T>'.
    *   Change 'at' method to take 'self: @Array::<T>' and return '@T'.

*   Update CASM code generation for array_get:
    *   Exclude the array from both success and failure branch outputs.

*   Update Sierra simulation of array_get:
    *   On success, return (RangeCheck, element).
    *   On failure, return (RangeCheck).

*   Update the fib_array example program (examples/fib_array.cairo):
    *   Dereference snapshot return values from 'at' calls when used in arithmetic or returned as felt values.

*   Revise generated Sierra IR for functions using array_get:
    *   Use 'Snapshot<Array<T>>' as input parameter type.
    *   Use 'core::option::Option::<@T>' as the result enum type.
    *   Exclude Array<T> from the function signature output.

*   Adjust execution cost of array_get:
    *   For felt elements, reduce cost to Const: 1170.
    *   For u256 elements, reduce cost to Const: 1380.

*   Update Sierra function signatures:
    *   'core::array::ArrayImpl::<core::felt>::at' should be (RangeCheck, Snapshot<Array<felt>>, u32) -> (RangeCheck, core::PanicResult::<@core::felt>).
    *   'core::array::array_at::<core::felt>' should be (RangeCheck, Snapshot<Array<felt>>, u32) -> (RangeCheck, core::PanicResult::<@core::felt>).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.