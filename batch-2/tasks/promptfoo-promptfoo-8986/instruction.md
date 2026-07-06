I'm running into a subtle but serious bug with inverted assertion types in my evaluation pipeline.

*   The matchesLlmRubric function must include metadata: { graderError: true } in the returned GradingResult for all error/failure paths, including: output that is neither a string nor an object (e.g., number, array, null), and any case where JSON extraction from the grader response fails.

*   When remote grading transport fails (e.g., a network error), matchesLlmRubric must return a GradingResult with pass: false, score: 0, a reason that contains 'Could not perform remote grading', and metadata: { graderError: true }.

*   When handleLlmRubric is called with params.inverse equal to true and the grader result has metadata.graderError equal to true, it must return the grader result verbatim with the assertion from params attached — it must NOT invert the pass or score values.

*   When handleLlmRubric is called with params.inverse equal to true and the grader result does NOT have metadata.graderError set to true, it must flip the pass boolean and compute the inverted score as (1 - score), clamping the result to [0, 1] and treating NaN scores as 0 before inversion.

*   Score inversion by handleLlmRubric must produce exact boundary results: a score of 1 inverts to 0, a score of 0 inverts to 1, an out-of-range score (e.g., 5) inverts and clamps to 0, and a NaN score is treated as 0 and inverts to 1.

*   When handleTrajectoryGoalSuccess is called with params.inverse equal to true and the inner grader result has metadata.graderError equal to true, it must preserve the failure state (pass: false, score: 0, reason unchanged) and attach the assertion from params — it must NOT invert the result.

*   The handleLlmRubric function is asynchronous; an invalid rendered value type (non-string, non-object) must cause the returned promise to reject with the error message: 'Invariant failed: "llm-rubric" assertion type must have a string or object value'.


*   Interface details: Type: Function
Name: matchesLlmRubric
Location: src/matchers/llmGrading.ts
Signature: matchesLlmRubric(rubric: string, llmOutput: string, grading: GradingConfig, options?: ...) -> Promise<GradingResult>
Description: Evaluates an LLM output against a rubric using an LLM grader. All error/failure paths must now return a GradingResult that includes metadata: { graderError: true }. This includes: malformed output (not a string or object), null output, array output, failed JSON extraction, and remote grading transport failures. Remote grading transport failures must return a GradingResult with pass: false, score: 0, reason containing 'Could not perform remote grading', and metadata: { graderError: true }.

Type: Function
Name: handleLlmRubric
Location: src/assertions/llmRubric.ts
Signature: handleLlmRubric(params: AssertionParams) -> Promise<GradingResult>
Description: Handles the llm-rubric assertion type. When params.inverse is true and the inner grader result has metadata.graderError equal to true, the function must return the grader result verbatim with the assertion attached (NOT inverted). When params.inverse is true and the grader result does NOT have metadata.graderError, the function must flip the pass boolean and invert the score as (1 - score), clamping the result to the range [0, 1] and treating NaN as 0. The function is async; invalid rendered value types cause the returned promise to reject with the error 'Invariant failed: "llm-rubric" assertion type must have a string or object value'.

Type: Function
Name: handleTrajectoryGoalSuccess
Location: src/assertions/trajectory.ts
Signature: handleTrajectoryGoalSuccess(params: AssertionParams) -> Promise<GradingResult>
Description: Handles the trajectory:goal-success assertion type. When params.inverse is true and the inner grader result has metadata.graderError equal to true, the function must return the grader result with the assertion attached and must NOT invert the result (pass must remain false, score must remain 0, reason must be preserved).

Type: Interface / Type
Name: GradingResult
Location: src/types/index.ts (or src/types.ts)
Signature: metadata?: { graderError?: boolean; [key: string]: unknown }
Description: The GradingResult type must support a metadata field that can include a boolean graderError property. All grader failure paths in matchesLlmRubric must populate metadata.graderError as true.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.