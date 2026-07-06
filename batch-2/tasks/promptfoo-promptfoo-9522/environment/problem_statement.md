## Description

When using the random sample filter to run a subset of test cases, every run selects a different subset of tests. This makes it impossible to reproduce a specific evaluation, compare outputs between runs, or share a reproducible benchmark with a colleague.

We need a way to "pin" the random selection so that the same subset of tests is always chosen when the same seed is provided. This would allow teams to lock down a repeatable sample for CI runs, regression testing, or side-by-side comparisons.

## Expected Behavior

- A numeric seed option should be available alongside the sample filter, both on the command line and in configuration files.
- When the same seed is provided, the exact same tests are selected on every run, regardless of how many times the evaluation is run.
- The seed can be specified in a configuration file and should be normalized automatically — for example, if a YAML file quotes the seed as a string, it should be coerced to a number.
- Configuration files with invalid seed values (non-numeric strings, fractional values, or out-of-range numbers) should be rejected with a clear error message indicating which config file is invalid.
- When scenario-based configs are used, each scenario should independently derive its own sampling stream from the seed, so different scenarios don't end up picking identical test subsets.
- The original configuration objects should not be mutated during sampling — a scenario's original test reference (such as an external file path) should remain unchanged after resolution.

## Why This Matters

Reproducible sampling is critical for CI workflows, benchmarking, and debugging. Without a seed option, it's impossible to rerun the exact same subset of tests, making it hard to isolate regressions or confirm fixes.
