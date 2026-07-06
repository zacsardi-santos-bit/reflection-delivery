## Description

The current mechanism for controlling whether circuit execution prints output is a simple boolean flag: either everything goes to standard output, or nothing does. This is too coarse-grained — it makes it impossible for callers to capture printed output for later use, which is particularly limiting for test infrastructure that wants to display each test's output after the test completes rather than having it appear inline and potentially interleaved with other concurrent output.

## Expected Behavior

- Callers should be able to choose from at least three output modes: suppress output entirely, send it directly to the terminal, or capture it into a string buffer that the caller owns.
- The output mode should be expressible as a proper type rather than a plain boolean, so it can carry the string buffer reference when capture mode is selected.
- The test execution function and the default foreign-call executor both need to accept this new output-mode type in place of the old boolean parameter.
- All existing call sites should be updated to use the new type, preserving their existing behavior.

## Why This Matters

With this change, it becomes possible to run circuit tests in parallel, collect each test's printed output separately, and then display it in a clean, ordered way — for example, only showing output after all results for a given test are collected. The previous boolean approach forced a choice between printing everything in real-time (causing interleaved output when tests run concurrently) or discarding all output.
