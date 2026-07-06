## Description

Biome currently lacks a lint rule to detect a common async JavaScript anti-pattern: using await expressions inside loop constructs. When a developer awaits a promise inside a loop body or loop condition, each iteration is forced to wait for the previous one to complete before starting. This turns what could be parallel asynchronous work into sequential execution, often leading to poor performance.

## Expected Behavior

A new lint rule should be added to the nursery category that:

- Warns when an await expression appears in the body or condition of while, for-of, for-in, regular for, and do-while loops
- Also warns when a for-await-of statement is nested inside another synchronous loop
- Includes a helpful suggestion to use concurrent promise resolution instead

The rule should also understand scope boundaries and NOT warn when the await is:
- Inside a nested function declaration, function expression, arrow function, or class method defined within the loop (these create their own async scope)
- In the iterable position of for-of, for-in, or for-await-of loops
- In the initialization part of a regular for loop
- Directly inside a for-await-of loop body at the top level (intentional async iteration)

## Why This Matters

This pattern can silently degrade application performance. Developers may not realize they are forcing sequential execution when parallel execution would work just as well. An automated lint rule catches this class of bug early in the development workflow.
