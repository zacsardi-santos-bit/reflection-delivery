Update the `expr` utility to handle long arithmetic expressions without crashing. Ensure that the utility can process a large number of operands and operators as separate arguments and return the correct result.

*   Modify the expression evaluator to prevent stack overflow:
    *   Implement an iterative approach instead of a recursive one to handle deep expression evaluation.
*   Ensure the utility handles long addition expressions:
    *   For non-Windows systems, the utility must correctly compute and output the sum of integers from 1 to 40000 as '800020000' followed by a newline.
    *   For Windows systems, due to command-line input limitations, the utility must correctly compute and output the sum of integers from 1 to 1300 as '845650' followed by a newline.
*   Prevent segmentation faults:
    *   Ensure the utility exits successfully without crashing regardless of the number of terms in the expression.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.