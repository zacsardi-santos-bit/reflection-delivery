I'm working on adding argument-level verification to trajectory-based AI agent evaluations.

*   Normalized trajectory steps must include an 'args' field containing the parsed arguments for each tool call step. The 'args' value must be populated from span attributes: 'tool.arguments' (parsed from JSON string), 'codex.mcp.input' (parsed from JSON string), or 'tool.args' (used directly if already an object, or parsed if a JSON string).

*   The handleTrajectoryToolArgsMatch function must be exported from src/assertions/trajectory.ts and accept an AssertionParams argument. It must return an object with fields: pass (boolean), score (1 or 0), reason (string), and assertion (the original assertion object).

*   The renderedValue for handleTrajectoryToolArgsMatch must support the following fields: 'name' (exact tool name), 'pattern' (glob pattern for tool name matching), 'args' or 'arguments' (expected argument object; both are accepted as aliases), and 'mode' ('partial' or 'exact'). Default mode when not specified is 'partial'.

*   handleTrajectoryToolArgsMatch must throw an error with the message 'trajectory:tool-args-match assertion must include an args or arguments property' when neither 'args' nor 'arguments' is present in the value.

*   handleTrajectoryToolArgsMatch must throw an error with the message 'trajectory:tool-args-match assertion mode must be "partial" or "exact"' when mode is set to any value other than 'partial' or 'exact'.

*   When a tool call is found and its args satisfy the expected arguments in partial mode, handleTrajectoryToolArgsMatch must return pass:true, score:1, and reason in the format: 'Tool "<nameOrPattern>" matched expected arguments (partial) on <alias>. Args: <JSON.stringify(actualArgs)>'.

*   When a tool call is found and its args satisfy the expected arguments in exact mode, handleTrajectoryToolArgsMatch must return pass:true, score:1, and reason in the format: 'Tool "<nameOrPattern>" matched expected arguments (exact) on <alias>. Args: <JSON.stringify(actualArgs)>'.

*   When a tool call for the named/pattern-matched tool is found but no args match the expected arguments, handleTrajectoryToolArgsMatch must return pass:false, score:0, and reason in the format: 'No call to tool "<nameOrPattern>" matched expected arguments (<mode>): <JSON.stringify(expectedArgs)>. Observed args: <JSON.stringify(observedArgs)>'.

*   When a matching tool is found in the trace but no arguments were captured for it, handleTrajectoryToolArgsMatch must return pass:false, score:0, and reason in the format: 'Tool "<nameOrPattern>" was observed but no arguments were captured. Actual tools: <aliases>'.

*   When inverse:true is set and a matching argument is found, handleTrajectoryToolArgsMatch must return pass:false, score:0, and reason in the format: 'Forbidden argument match for tool "<nameOrPattern>" was observed on <alias>. Args: <JSON.stringify(actualArgs)>'.

*   The getAttributesForItem method on OpenAICodexSDKProvider must serialize the 'input' field of mcp_tool_call items to JSON and include it as the 'codex.mcp.input' attribute. Sensitive fields (such as email, apiKey, password, Authorization, and similar) must be replaced with '[REDACTED]' before serialization. This redaction must apply recursively to nested objects.

*   The getCompletionAttributesForItem method on OpenAICodexSDKProvider must also serialize and redact the 'input' field for mcp_tool_call items into 'codex.mcp.input'. When the input is a JSON string that parses to an object, it must be parsed, redacted, and re-serialized. When the input is a plain string that is not a parseable JSON object, the entire value must be replaced with '[REDACTED]'.


*   Interface details: Type: Function
Name: handleTrajectoryToolArgsMatch
Location: src/assertions/trajectory.ts
Signature: handleTrajectoryToolArgsMatch(params: AssertionParams) -> { pass: boolean, score: number, reason: string, assertion: Assertion }
Description: Handles the 'trajectory:tool-args-match' assertion type. Checks whether any tool call in the trace matches the specified tool name (or pattern) with the expected arguments. Supports 'partial' (default) and 'exact' matching modes, and inverse assertions. Must be exported alongside handleTrajectoryToolSequence, handleTrajectoryToolUsed, and handleTrajectoryStepCount. Must also be registered in src/assertions/index.ts under the key 'trajectory:tool-args-match', and the assertion type must be added to the BaseAssertionTypesSchema in src/types/index.ts.

The renderedValue for this handler accepts:
  - name?: string            — exact tool name to match
  - pattern?: string         — glob pattern for tool name matching
  - args?: unknown           — expected arguments object
  - arguments?: unknown      — alias for args (used when 'args' key is not present)
  - mode?: 'partial' | 'exact'  — default 'partial'

Exact reason strings returned:
  - Pass (partial): 'Tool "<name>" matched expected arguments (partial) on <step>. Args: <JSON>'
  - Pass (exact): 'Tool "<name>" matched expected arguments (exact) on <step>. Args: <JSON>'
  - Fail (no tool match): 'No tool call matched "<name>". Actual tools: <list>'
  - Fail (no args captured): 'Tool "<name>" was observed but no arguments were captured. Actual tools: <list>'
  - Fail (args mismatch): 'No call to tool "<name>" matched expected arguments (<mode>): <expectedJSON>. Observed args: <observedJSON>'
  - Fail (inverse match found): 'Forbidden argument match for tool "<name>" was observed on <step>. Args: <JSON>'
  - Pass (inverse, tool not found): 'Forbidden argument match for tool "<name>" was not observed because no tool call matched it'
  - Pass (inverse, no args match): 'Forbidden argument match for tool "<name>" was not observed. Observed args: <list>'

Where <step> is formatted as 'tool:<toolName>' and <list> is formatted as 'tool:<name1>, tool:<name2>'.

Error strings thrown:
  - 'trajectory:tool-args-match assertion must include an args or arguments property'
  - 'trajectory:tool-args-match assertion mode must be "partial" or "exact"'

Type: Function
Name: formatTrajectoryArgs
Location: src/assertions/trajectoryUtils.ts
Signature: formatTrajectoryArgs(args: unknown) -> string
Description: Serializes trajectory step args to a string for use in reason messages. Returns JSON.stringify of the args when possible; falls back to String(args). Returns '(none)' for undefined.

Type: Interface field
Name: args
Location: src/assertions/trajectoryUtils.ts (TrajectoryStep interface)
Description: Optional field added to the TrajectoryStep interface. Type: unknown. Populated during step extraction from tool-type spans by reading span attributes (checked in priority order): 'tool.arguments', 'tool.args', 'tool.input', 'tool_arguments', 'tool_args', 'tool_input', 'function.arguments', 'function.args', 'function.input', 'function_arguments', 'function_args', 'gen_ai.tool.arguments', 'gen_ai.tool.args', 'gen_ai.tool.input', 'gen_ai.tool.call.arguments', 'gen_ai.tool.call.args', 'agent.tool.arguments', 'agent.tool.args', 'agent.tool.input', 'codex.mcp.arguments', 'codex.mcp.args', 'codex.mcp.input', 'arguments', 'args', 'input'. String attribute values are parsed as JSON when possible.

Type: Method
Name: getAttributesForItem
Location: src/providers/openai/codex-sdk.ts (on OpenAICodexSDKProvider class)
Signature: getAttributesForItem(item: object) -> Record<string, string>
Description: Existing private method on OpenAICodexSDKProvider. For mcp_tool_call items, must now serialize the item's input/args/arguments field to JSON and include it as the 'codex.mcp.input' attribute. Sensitive fields (e.g., email, apiKey, password, Authorization) must be replaced with '[REDACTED]' recursively before serialization. Plain strings that match email address patterns must also be replaced with '[REDACTED]'.

Type: Method
Name: getCompletionAttributesForItem
Location: src/providers/openai/codex-sdk.ts (on OpenAICodexSDKProvider class)
Signature: getCompletionAttributesForItem(item: object) -> Record<string, string>
Description: Existing private method on OpenAICodexSDKProvider. For mcp_tool_call items, must now serialize and redact the item's input/args/arguments field into the 'codex.mcp.input' attribute. When input is a string: if parseable as a JSON object, parse then redact then re-serialize; if it matches an email address pattern or cannot be parsed as a structured JSON object, the value must be replaced with '[REDACTED]'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.