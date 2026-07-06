## Description

When an LLM-based grader fails to produce a valid response — due to network errors, malformed output, or JSON parsing failures — the evaluation system has no reliable way to distinguish that failure from a genuine "did not pass" result. This becomes a critical bug with inverted ("not-") assertion types: a grader error that returns a failing result gets flipped to a passing result by the inversion logic, producing a false positive.

## Expected Behavior

- All grader error/failure paths (malformed output, null output, array output, failed JSON extraction, remote transport failures) should include a distinguishing flag in their result metadata so downstream logic can detect them.
- When an inverted assertion type receives a grader failure that has this flag set, it must preserve the failure state as-is rather than inverting it to a pass.
- This behavior should apply consistently across all LLM-rubric-based assertion types, including trajectory goal success assertions.
- Remote grading transport failures (e.g., network errors) should also be treated as tagged grader errors and include a human-readable reason indicating remote grading could not be performed.

## Why This Matters

Without this fix, any network or parsing failure in the grader causes inverted assertions to produce incorrect passing results that appear to validate the output. This undermines the reliability of red-team and other evaluation pipelines that depend on these inverted assertion types to catch problems.
