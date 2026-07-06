Improve the Common Subexpression Elimination (CSE) optimization pass in the Sway compiler's intermediate representation optimizer to recognize and eliminate redundant address-loading instructions. Ensure that the CSE pass treats these instructions as congruent when they refer to the same target and updates all downstream uses accordingly.

*   Update the CSE optimization pass to treat redundant address-loading instructions as congruent:
    *   Recognize two address-of-local instructions targeting the same local variable as congruent.
    *   Apply the same congruence rule to address-of-global, address-of-configurable, and address-of-storage-key instructions.
*   Rewrite all uses of redundant instructions to reference the surviving instruction:
    *   Ensure that any pointer operations using the redundant load as their base are updated to use the first instruction.
*   Report that the Intermediate Representation (IR) was modified when congruence-based replacements are made:
    *   Indicate `Modified: true` in the transformation result when replacements occur.
*   Validate the CSE pass behavior with a new test:
    *   Create a test file `sway-ir/tests/cse/cse4.ir` containing a script with two `get_local` instructions for the same local.
    *   Use filecheck assertions to verify that the second `get_elem_ptr`'s base is rewritten to use the first `get_local`'s result.
*   Capture the expected transformed IR in a snapshot:
    *   Ensure `sway-ir/tests/cse/cse4.ir.snap` reflects `Modified: true` and shows the second `get_elem_ptr` rewritten from the second `get_local` to the first.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.