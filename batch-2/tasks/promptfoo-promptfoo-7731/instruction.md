I'm trying to use the post-test lifecycle hook in the extension system to attach custom scores and metadata to my evaluation results, but it doesn't seem to work.

*   When runExtensionHook is called with hook name 'afterEach', it must merge the `namedScores` object from the extension's return value into the result's existing `namedScores` using a shallow merge. Only finite numeric values are accepted; non-numeric values (strings, null, arrays, objects), NaN, Infinity, and -Infinity must be silently filtered out.

*   When runExtensionHook is called with hook name 'afterEach', it must merge the `metadata` object from the extension's return value into the result's existing `metadata` using a shallow merge, preserving any existing keys not returned by the extension.

*   When runExtensionHook is called with hook name 'afterEach', it must merge the `response.metadata` object from the extension's return value into the result's existing `response.metadata` using a shallow merge, while preserving all other fields on `response` (such as `response.output`) unchanged.

*   When runExtensionHook is called with hook name 'afterEach' and the extension returns undefined or nothing, the existing `namedScores`, `metadata`, and `response` must be preserved unchanged.

*   When runExtensionHook is called with hook name 'afterEach', extensions must NOT be able to override the `success`, `score`, or `response.output` fields on the result, regardless of what the extension returns.

*   When runExtensionHook chains multiple 'afterEach' extensions, each extension must receive the already-merged context (incorporating all prior extensions' contributions) as its input, not the original unmodified context.

*   When runExtensionHook chains multiple 'afterEach' extensions, the final result must accumulate contributions from all extensions; when multiple extensions set the same key, the last extension's value wins.

*   When runExtensionHook is called with hook name 'afterEach' and an extension throws an error, the function must return the original unmodified context rather than propagating the error or losing the result.

*   When runExtensionHook chains multiple 'beforeAll' extensions, each extension must receive the accumulated state from all prior extensions as its input context (not the original unmodified context).

*   The AfterEachExtensionHookContext type already exists and is exported from src/evaluatorHelpers.ts. It has a `test` field (TestCase) and a `result` field (EvaluateResult) which exposes `namedScores`, `metadata`, `response` (with optional `metadata` sub-field and `output`), `success`, and `score`.

*   The EvaluateSummaryV3 type already exists and is exported from src/types/index.ts. It exposes `prompts` (array with `metrics?.namedScores`), `results` (array with `metadata`, `namedScores`, `response?.metadata`, and `success`), and `stats` (with `successes` count).

*   When the evaluate function runs a test suite with afterEach extension hooks, the hook-merged namedScores must be persisted into both the result's `namedScores` and the prompt's `metrics.namedScores` in the evaluation summary. Hook-merged metadata must appear in the result's `metadata`, and hook-merged response metadata must appear in the result's `response.metadata`.

*   When the evaluate function runs a test suite with an afterEach extension hook that throws an error, the result must still be persisted with its original data — the evaluation must not lose the row, and the original success status and stats must remain accurate.


*   Interface details: Type: Function
Name: runExtensionHook
Location: src/evaluatorHelpers.ts
Signature: runExtensionHook(extensions: string[], hookName: string, context: unknown) -> Promise<unknown>
Description: Already exists. Needs enhanced behavior for two hook types:

For `afterEach` hooks — when an extension returns a value, the function must shallow-merge the extension's `result.namedScores`, `result.metadata`, and `result.response.metadata` into the accumulated context result. Only finite numeric values from `namedScores` should be kept (strings, null, arrays, objects, NaN, Infinity, and -Infinity must be filtered). The fields `success`, `score`, and `response.output` must not be overridable. If the extension returns undefined/null, the context is preserved unchanged. Each extension in the chain must receive the already-merged context from all prior extensions as its input (not the original context).

For `beforeAll` hooks — each extension must also receive the accumulated (already-merged) context from prior extensions as input, not the original context.

Type: Type
Name: AfterEachExtensionHookContext
Location: src/evaluatorHelpers.ts
Description: Already exists and is exported. Must remain exported. Shape: `{ test: TestCase, result: EvaluateResult }`. Used as the type annotation in afterEach extension hook code paths.

Note: `EvaluateSummaryV3` referenced in the test is also already exported from `src/types/index.ts` and does not need to be modified.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.