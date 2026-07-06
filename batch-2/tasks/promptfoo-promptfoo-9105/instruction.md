I'm working on improving the prompt suggestion feature in this project.

*   The generatePrompts function must accept a second argument specifying how many prompt variants to generate. It must validate that this count is a positive integer between 1 and 50 (inclusive). For invalid counts (zero, negative, fractional, NaN, or above 50), the function must return immediately without calling any provider, setting prompts to undefined and error to a string that contains the text 'num must be an integer between 1 and 50'.

*   When generatePrompts is called with a valid count N, it must call the provider N times and accumulate token usage across all calls. The return shape is { prompts?: string[], error?: string, tokensUsed: TokenUsage }.

*   When some but not all provider calls fail in generatePrompts, the function must return only the successfully generated prompt variants in the prompts array with no error field set (partial success).

*   When all provider calls fail (returning an error response or throwing an exception), generatePrompts must return prompts as undefined and error as a semicolon-separated string in the format 'Variant 1: <error1>; Variant 2: <error2>' for each numbered variant.

*   A constant MAX_SUGGESTIONS_COUNT with value 50 must be exported from src/types/index.ts and re-exported as a named export from src/index.ts.

*   The CommandLineOptionsSchema must include an optional suggestionsCount field that coerces string input to a number and validates that the value is a positive integer between 1 and MAX_SUGGESTIONS_COUNT (50) inclusive. It must reject values of 0, negative values, values above MAX_SUGGESTIONS_COUNT, fractional values, and non-numeric strings by throwing a validation error.

*   When the evaluate() function is called with generateSuggestions: true and a suggestionsCount option, it must pass that count to generatePrompts. If suggestionsCount is omitted it must default to 1. If suggestionsCount exceeds MAX_SUGGESTIONS_COUNT (50) it must be clamped to 50. If suggestionsCount is an invalid value such as 0 it must be coerced to 1.

*   The eval options processing logic must preserve evaluateOptions.suggestionsCount from the config file in the resulting options object.

*   The eval options processing logic must respect commandLineOptions.suggestionsCount from the config file, and commandLineOptions.suggestionsCount must override evaluateOptions.suggestionsCount.

*   When the CLI suggestPrompts option is provided as a positive integer N, the resulting options must have generateSuggestions set to true and suggestionsCount set to N. This takes precedence over an explicit generateSuggestions=false.

*   An explicit generateSuggestions=false passed directly as a runtime option must override commandLineOptions.generateSuggestions=true from the config file. In this case suggestionsCount must be undefined in the resulting options.

*   An explicit generateSuggestions=false passed as a runtime option must override evaluateOptions.generateSuggestions=true from the config file while still preserving the evaluateOptions.suggestionsCount value in the resulting options.

*   When commandLineOptions.generateSuggestions=false is set in the config file, it must override evaluateOptions.generateSuggestions=true from the same config, while still preserving the evaluateOptions.suggestionsCount value in the resulting options.

*   When suggestionsCount is passed directly as a runtime option alongside generateSuggestions=true (e.g., from non-Commander callers), both values must be preserved as-is in the resulting options.


*   Interface details: Type: Function
Name: generatePrompts
Location: src/suggestions.ts
Signature: generatePrompts(prompt: string, num: number) -> Promise<{ prompts?: string[], error?: string, tokensUsed: TokenUsage }>
Description: Generates `num` alternative prompt variants for the given prompt string. The `num` parameter must be a positive integer between 1 and MAX_SUGGESTIONS_COUNT (50) inclusive. For invalid `num` values (0, negative, fractional, NaN, or above 50), the function returns immediately without calling the provider, with `prompts` set to undefined and `error` containing the string "num must be an integer between 1 and 50". For valid `num`, the provider is called `num` times and token usage is accumulated across all calls. If at least one call succeeds, `prompts` contains the successful outputs and `error` is undefined. If all calls fail (via error response or thrown exception), `prompts` is undefined and `error` is a semicolon-separated string in the format "Variant 1: <error1>; Variant 2: <error2>".

Type: Constant
Name: MAX_SUGGESTIONS_COUNT
Location: src/types/index.ts
Signature: MAX_SUGGESTIONS_COUNT: number (value: 50)
Description: The maximum number of prompt variants that can be requested. Must be exported from src/types/index.ts and re-exported from src/index.ts as a named export.

Type: Schema field (addition to existing CommandLineOptionsSchema)
Name: suggestionsCount (field on CommandLineOptionsSchema)
Location: src/types/index.ts
Signature: suggestionsCount: optional integer, 1 <= value <= MAX_SUGGESTIONS_COUNT
Description: An optional field on CommandLineOptionsSchema that specifies the number of prompt suggestions to generate. It coerces string input (e.g., '5') to a number. It rejects (throws) values of 0, negative values, values above MAX_SUGGESTIONS_COUNT, non-integer values (e.g., 1.5), and non-numeric strings (e.g., 'abc'). It accepts boundary values 1 and MAX_SUGGESTIONS_COUNT.

Type: Options field (addition to existing evaluate options)
Name: suggestionsCount (field on EvaluateOptions / options passed to evaluate)
Location: src/evaluator.ts (or the evaluate options type)
Signature: suggestionsCount?: number
Description: When evaluate() is called with generateSuggestions: true, it passes the suggestionsCount value to generatePrompts. If suggestionsCount is omitted, defaults to 1. If suggestionsCount exceeds MAX_SUGGESTIONS_COUNT (50), it is clamped to 50. If suggestionsCount is invalid (e.g., 0), it is coerced to 1.

Type: Config field (additions to config merging logic)
Name: suggestionsCount (in evaluateOptions and commandLineOptions config sections)
Location: src/commands/eval/ (eval command options processing)
Description: The suggestionsCount field is read from both evaluateOptions and commandLineOptions sections of the config file. commandLineOptions.suggestionsCount overrides evaluateOptions.suggestionsCount. The CLI suggestPrompts option (a positive integer) sets generateSuggestions=true and suggestionsCount to that integer, overriding even an explicit generateSuggestions=false. An explicit generateSuggestions=false passed directly as a CLI option overrides commandLineOptions.generateSuggestions=true from the config file (and results in suggestionsCount being undefined), but also overrides evaluateOptions.generateSuggestions=true while preserving the evaluateOptions.suggestionsCount value. commandLineOptions.generateSuggestions=false from config overrides evaluateOptions.generateSuggestions=true from config while preserving evaluateOptions.suggestionsCount.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.