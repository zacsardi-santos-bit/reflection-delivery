## Description

The expr utility crashes with a segfault when given a very long arithmetic expression. For example, summing thousands of numbers passed as individual arguments causes the program to overflow the call stack and terminate abnormally instead of computing the correct result.

## Expected Behavior

- When provided with a large number of operands and operators as separate arguments (e.g., adding thousands of numbers), the utility should complete successfully and print the correct result.
- The utility should not crash or produce a segfault regardless of how many terms are in the expression.

## Current Behavior

Passing a sufficiently long expression (such as the sum of all integers from 1 to 40,000) causes a segfault due to deep recursion in the expression evaluator exhausting the native call stack.

## Why This Matters

Users relying on this utility for scripting need it to handle arbitrarily long expressions gracefully. A crash instead of a result breaks pipelines and scripts that generate dynamic arithmetic expressions with many terms.
