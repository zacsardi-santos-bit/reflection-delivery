## Description

When writing JavaScript or TypeScript test files, it's good practice to place lifecycle hooks (setup/teardown functions) before test cases in the same block. This way, anyone reading the test file immediately sees what runs before and after each test, rather than having to scan through a list of test cases to find the setup code.

Currently, the linter does not enforce this convention. Developers can accidentally write a test case and then add a hook below it, which makes the file harder to read and reason about.

## Expected Behavior

A new lint rule should be added that detects when a lifecycle hook appears **after** a test case in the same scope — whether that scope is a describe block or the top level of the file. The rule should:

- Flag each misplaced hook individually, pointing to both the hook and the test case it follows
- Include a message suggesting the hook be moved above all test cases in the block
- Apply at every nesting level independently — an inner describe block's ordering is checked regardless of the outer block's ordering
- Only flag hooks that appear after at least one test; hooks placed before all tests should not be flagged

## Why This Matters

Without this rule, test files can slowly accumulate hooks in arbitrary positions, making the setup/teardown flow invisible at first glance. Enforcing this ordering improves readability and helps developers immediately understand what state each test runs in.
