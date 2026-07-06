I'm working on making evaluation results display variable columns in a stable, predictable order.

*   The ResultsFile type must include an optional top-level `vars` field of type `string[]` representing the persisted variable column order for that evaluation.

*   convertResultsToTable must use ResultsFile.vars as the ordered prefix for result.head.vars when that field is a non-empty array; row values in result.body[n].vars must be ordered to match head.vars, with missing variable values filled as empty string.

*   Duplicate entries in the persisted ResultsFile.vars list must be deduplicated so that no column name appears more than once in result.head.vars.

*   Variables that appear only in response.metadata.transformDisplayVars (and are not present in the result's own vars object) must be appended to result.head.vars after the persisted prefix, sorted alphabetically.

*   Runtime-only keys present in result.vars but absent from the persisted ResultsFile.vars list must be appended to result.head.vars after the persisted prefix in alphabetical order.

*   Falsy values in result.vars (empty string, 0, false) must NOT be overwritten by a matching key in transformDisplayVars; they must be rendered as their string representation ('', '0', 'false' respectively).

*   When transformDisplayVars contains a key that also exists in result.vars with a different non-falsy value, a debug-level log message must be emitted that contains the substring "key '<varname>' collides" (where <varname> is the actual colliding key name).

*   When ResultsFile.vars is absent, convertResultsToTable must fall back to alphabetical ordering of all variable keys for result.head.vars.

*   When ResultsFile.vars is present but is not an array (malformed/corrupt data), convertResultsToTable must treat it as absent and fall back to alphabetical ordering.

*   The Eval class constructor must accept a second options argument with an optional `vars: string[]` field; when provided, this value must be stored and accessible as eval.vars.

*   Eval.toResultsFile() must include a `vars` field equal to eval.vars in its output when eval.vars is non-empty, and must omit the `vars` field entirely when eval.vars is empty.

*   EvalQueries must expose a getVarsFromEvals function that accepts an array of Eval objects and returns a Promise resolving to a Record<string, string[]> mapping each eval's ID to an alphabetically sorted list of variable key names extracted from that eval's stored test results; it must return an empty object for an empty input array and must omit evals whose test results contain no vars data.

*   When evaluate() runs test rows concurrently and rows complete out of order, evalRecord.vars must preserve the variable key order as they appear sequentially across testSuite.tests[].vars (i.e., the first new key encountered wins its position, regardless of completion order).

*   When Eval.getTablePage() is called on an eval whose persisted vars list is empty, it must backfill vars from the stored test results (sorting keys alphabetically), and any metadata-only sessionId column must be appended after the backfilled vars in the returned result.head.vars.


*   Interface details: Type: Function
Name: convertResultsToTable
Location: src/util/convertEvalResultsToTable.ts
Signature: convertResultsToTable(resultsFile: ResultsFile) -> EvaluateTable
Description: Converts a ResultsFile to a table structure with head.vars and body[].vars. Must use ResultsFile.vars (if present and a valid array) as the ordered prefix for column headers, deduplicate it, append runtime-only and transform-display-only vars alphabetically after the prefix, preserve falsy var values, and emit a debug log when transformDisplayVars collides with a different existing value. Falls back to alphabetical ordering when ResultsFile.vars is absent or not an array.

Type: Interface
Name: ResultsFile
Location: src/types/index.ts
Description: The serialized representation of an evaluation's results. Must include an optional `vars` field of type `string[]` representing the persisted variable column order.
Signature: vars?: string[]

Type: Class
Name: Eval
Location: src/models/eval.ts
Description: Represents an evaluation record. The constructor must accept a second options argument with an optional `vars: string[]` field. The `vars` property must be accessible on instances. The `toResultsFile()` method must include `vars` in output when non-empty, and omit it when empty. The `getTablePage()` method must backfill `this.vars` from stored results when `this.vars` is empty.
Signature:
  constructor(config: object, options?: { vars?: string[]; [key: string]: unknown })
  vars: string[]
  toResultsFile(): Promise<ResultsFile>
  getTablePage(params: { filters: unknown[] }): Promise<{ head: { vars: string[] }; [key: string]: unknown }>
  addResult(row: unknown): Promise<void>

Type: Class
Name: EvalQueries
Location: src/models/eval.ts
Description: Contains database query helpers for evaluations. Must expose a getVarsFromEvals static method that queries variable keys from stored test result JSON.
Signature:
  static getVarsFromEvals(evals: Eval[]): Promise<Record<string, string[]>>

Type: Function
Name: evaluate
Location: src/evaluator.ts
Signature: evaluate(testSuite: TestSuite, evalRecord: Eval, options: { maxConcurrency?: number; [key: string]: unknown }) -> Promise<unknown>
Description: Runs the evaluation. When rows complete out of order due to concurrency, evalRecord.vars must end up reflecting the variable key order as encountered sequentially across testSuite.tests[].vars (first-seen order wins position), not the order rows completed.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.