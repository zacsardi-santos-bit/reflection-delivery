I'm building evaluations for an agentic AI system that uses distributed tracing to record execution steps — things like tool calls, command executions, searches, and reasoning steps.

*   The extractTrajectorySteps function must accept a TraceData object and return an array of normalized trajectory steps. Each step must have a 'type' field ('tool', 'command', 'reasoning', 'search', or 'span') and a 'name' field. Steps derived from tool call spans (via 'tool.name' attribute or 'codex.mcp.tool' attribute for MCP tool calls) must have type 'tool'. Steps from command execution spans must have type 'command'. Spans whose name matches a reasoning pattern (e.g. starts with 'reasoning') must have type 'reasoning' with the span name as the name. Spans with a 'query' attribute whose span name looks search-like must be typed 'search' with the query as the name; spans with a 'query' attribute that don't look search-like (e.g. a SQL query span) must fall back to type 'span'.

*   Steps returned by extractTrajectorySteps must include an 'aliases' array. Command steps must include the first word of the command as an alias (e.g. 'ls' for 'ls -la'). MCP tool call steps must include the original span name as an alias (e.g. 'mcp inventory/search_inventory').

*   The summarizeTrajectoryForJudge function must accept a TraceData object and return a pretty-printed JSON string (with whitespace, such that a key and value appear separated by a space, e.g. '"stepCount": 3'). The object must include 'stepCount' (total steps before compaction), 'compactedStepCount' (after collapsing consecutive identical steps), and 'steps' (array of step entries). Consecutive identical steps must be compacted into one entry with a 'collapsedCount' field. Each step entry must include 'index' (1-based position in the original uncompacted sequence), 'type', 'name', and optionally 'spanName' (when span name differs from the step name) and 'collapsedCount'.

*   The steps array in the output of summarizeTrajectoryForJudge must contain at most 25 entries. When the total compacted step count exceeds 24, truncation is applied: the first 12 compacted steps (head) and last 12 compacted steps (tail) are kept, and an object of the form { omittedCount: N } is inserted at 0-based position 12 between them, where N = compactedStepCount - 24. The last step in the original compacted sequence must always appear as the last entry in the steps array.

*   The handleTrajectoryToolUsed function must accept an AssertionParams object and return a GradingResult synchronously. When the assertion value is a string naming a tool, it must return pass=true, score=1 when the tool is found, with reason 'Observed required tool(s): <tool>. Actual tools: <tool-list>'. When inverse=true and the tool was used, it must return pass=false, score=0 with reason 'Forbidden tool(s) were used: <tool>. Actual tools: <tool-list>'. The tool list format is 'type:name' for each step (e.g. 'tool:search_orders').

*   When the handleTrajectoryToolUsed assertion value is an array and inverse=true, the assertion fails (pass=false, score=0) if any of the listed tools were actually used. The reason must identify only the tools from the list that were actually found: 'Forbidden tool(s) were used: <found-tools>. Actual tools: <tool-list>'.

*   When the handleTrajectoryToolUsed assertion value is an object with 'pattern', 'min', and 'max' fields, the handler must match tools using glob-style pattern matching and check that the count of matches is within [min, max]. On success: reason 'Matched tool "<pattern>" <count> time(s) (expected <min>-<max>). Matches: <matched-tools>'.

*   The handleTrajectoryToolSequence function must accept an AssertionParams object and return a GradingResult synchronously. When the value is an array of tool names, it must pass if those tools appear in that order (as a subsequence) in the trajectory, with reason 'Observed tool sequence in order: <tools>. Actual tools: <tool-list>'.

*   When the handleTrajectoryToolSequence assertion value is an object with 'mode: exact' and 'steps' array, it must fail if the actual tool list does not match exactly, with reason 'Expected exact tool sequence of <steps>, but actual tools were <tool-list>'.

*   When a step in the handleTrajectoryToolSequence sequence is an object that lacks both 'name' and 'pattern' fields, the function must throw an error with message 'trajectory:tool-sequence assertion step <N> must include a name or pattern property' (where N is the 1-based index of the invalid step).

*   The handleTrajectoryStepCount function must accept an AssertionParams object and return a GradingResult synchronously. When the value has a 'type' field with 'min' and 'max', it must count steps of that type and check that the count is within [min, max], with reason 'Matched <count> trajectory step(s) for type=<type> (expected <min>-<max>). Matches: <step-list>'.

*   When the handleTrajectoryStepCount assertion value has a 'pattern' field with only a 'min' (no 'max'), it must match step names by glob pattern and check count >= min, with reason 'Matched <count> trajectory step(s) for pattern=<pattern> (expected at least <min>). Matches: <step-list>'.

*   The handleTrajectoryGoalSuccess function must accept an AssertionParams object and return a Promise resolving to a GradingResult. It must extract the goal from either a string value directly, or from an object value with a 'goal' property. When the value is an object without a 'goal' property, it must throw with message 'trajectory:goal-success assertion must have a string value or an object with a goal property'.

*   When inverse=true and the underlying goal-success matcher returns pass=true, handleTrajectoryGoalSuccess must return { pass: false, score: 0, reason: 'Agent unexpectedly achieved the goal: <goal>', assertion }.

*   The matchesTrajectoryGoalSuccess function must be exported from src/matchers. It must accept (goal: string, trajectory: string, output: string, grading: GradingConfig | undefined, vars?: Record<string,unknown>, assertion?: Assertion, providerCallContext?: object) and return a Promise resolving to a GradingResult. It must throw 'Cannot grade output without grading config' when grading is undefined.

*   The matchesTrajectoryGoalSuccess function must render 'goal', 'trajectory', and 'output' as template variables into the rubric prompt, using the prompt label 'trajectory:goal-success'. Caller-supplied vars that use the keys 'goal', 'trajectory', or 'output' must be overridden by the actual argument values and must not spoof them. Additional caller vars must be merged and available as template variables.

*   The GradingResult returned by matchesTrajectoryGoalSuccess must include: pass, score, reason, assertion (the Assertion object or undefined), metadata.renderedGradingPrompt (the fully rendered prompt string), and tokensUsed with fields { total, prompt, completion, cached: 0, numRequests: 0, completionDetails: { reasoning: 0, acceptedPrediction: 0, rejectedPrediction: 0 } }.

*   When matchesTrajectoryGoalSuccess is called with an assertion that has a 'threshold' field, the result must return pass=false if the score returned by the grading provider is less than the threshold value, regardless of the provider's own pass/fail determination.


*   Interface details: Type: Function
Name: extractTrajectorySteps
Location: src/assertions/trajectoryUtils.ts
Signature: extractTrajectorySteps(trace: TraceData) -> TrajectoryStep[]
Description: Accepts a TraceData object and returns an array of normalized trajectory steps. Each step has a 'type' field ('tool', 'command', 'reasoning', 'search', or 'span'), a 'name' field, and an 'aliases' string array. Classification is based on span attributes: 'tool.name' → type='tool'; 'codex.item.type'='mcp_tool_call' with 'codex.mcp.tool' → type='tool' with original span name as alias; 'codex.item.type'='command_execution' with 'codex.command' → type='command' with first word of command as alias; spans whose name starts with 'reasoning' → type='reasoning'; 'query' attribute on a search-like span → type='search' with query as name; otherwise type='span'.

Type: Function
Name: summarizeTrajectoryForJudge
Location: src/assertions/trajectoryUtils.ts
Signature: summarizeTrajectoryForJudge(trace: TraceData) -> string
Description: Accepts a TraceData object and returns a pretty-printed JSON string (with spaces after colons and newlines with indentation, e.g. the output contains '"stepCount": 3' with a space). The JSON object contains 'stepCount' (total steps before compaction), 'compactedStepCount' (after collapsing consecutive identical steps), and 'steps' (array of at most 25 entries). Each step entry includes 'index' (1-based position in the original uncompacted sequence), 'type', 'name', and optionally 'spanName' (when different from name) and 'collapsedCount'. When the compacted step count exceeds 24, an { omittedCount: N } placeholder is inserted at 0-based position 12 in the steps array (where N = compactedStepCount - 24), with the first 12 compacted steps before it and the last 12 after it.

Type: Function
Name: handleTrajectoryToolUsed
Location: src/assertions/trajectory.ts
Signature: handleTrajectoryToolUsed(params: AssertionParams) -> GradingResult
Description: Handles the 'trajectory:tool-used' assertion type. Returns a synchronous GradingResult with pass, score (1 or 0), reason, and assertion fields. Supports string values (single tool check), array values (multiple tools), and object values with { pattern, min, max } for glob-based count matching. Supports inverse mode via params.inverse=true. Each tool step is formatted as 'type:name' (e.g. 'tool:search_orders') in reason strings.

Type: Function
Name: handleTrajectoryToolSequence
Location: src/assertions/trajectory.ts
Signature: handleTrajectoryToolSequence(params: AssertionParams) -> GradingResult
Description: Handles the 'trajectory:tool-sequence' assertion type. Returns a synchronous GradingResult. Array values check that tools appear in order as a subsequence. Object values with { mode: 'exact', steps } check that the tool list matches exactly. Throws if any step object lacks both 'name' and 'pattern' properties.

Type: Function
Name: handleTrajectoryStepCount
Location: src/assertions/trajectory.ts
Signature: handleTrajectoryStepCount(params: AssertionParams) -> GradingResult
Description: Handles the 'trajectory:step-count' assertion type. Returns a synchronous GradingResult. Accepts object values with { type?, pattern?, min, max? } and counts matching steps. When only 'min' is provided (no 'max'), the reason says 'expected at least N'. When both 'min' and 'max' are provided, the reason says 'expected min-max'.

Type: Function
Name: handleTrajectoryGoalSuccess
Location: src/assertions/trajectory.ts
Signature: handleTrajectoryGoalSuccess(params: AssertionParams) -> Promise<GradingResult>
Description: Handles the 'trajectory:goal-success' assertion type. Extracts the goal from a string value or an object with a 'goal' property. Throws 'trajectory:goal-success assertion must have a string value or an object with a goal property' when neither is provided. Calls matchesTrajectoryGoalSuccess with the goal, summarized trajectory, output, grading config, vars, assertion, and providerCallContext. When inverse=true and the matcher returns pass=true, returns { pass: false, score: 0, reason: 'Agent unexpectedly achieved the goal: <goal>', assertion }.

Type: Function
Name: matchesTrajectoryGoalSuccess
Location: src/matchers.ts
Signature: matchesTrajectoryGoalSuccess(goal: string, trajectory: string, output: string, grading: GradingConfig | undefined, vars?: Record<string, unknown>, assertion?: Assertion, providerCallContext?: object) -> Promise<GradingResult>
Description: Grades whether an agent achieved a goal by rendering goal, trajectory, and output as template variables into the grading rubric prompt (label: 'trajectory:goal-success') and calling the grading provider. Throws 'Cannot grade output without grading config' when grading is undefined. Caller-supplied 'goal', 'trajectory', and 'output' keys in vars are overridden by the actual argument values. Returns { pass, score, reason, tokensUsed: { total, prompt, completion, cached: 0, numRequests: 0, completionDetails: { reasoning: 0, acceptedPrediction: 0, rejectedPrediction: 0 } }, metadata: { renderedGradingPrompt }, assertion }. When assertion.threshold is set and score < threshold, returns pass=false.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.