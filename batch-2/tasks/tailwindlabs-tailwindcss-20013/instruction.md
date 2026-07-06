I'm working on improving how generated CSS is formatted in our test utilities.

*   A new function named 'pretty' must be exported from 'packages/tailwindcss/src/test-utils/run.ts'.

*   The 'pretty' function must accept a single string parameter; it must trim the input, and if the trimmed result is empty return an empty string (''), otherwise return the trimmed content wrapped with a leading newline and a trailing newline (i.e., '\n' + trimmedInput + '\n').

*   The existing 'run' function in 'packages/tailwindcss/src/test-utils/run.ts' must be updated to return the output of 'pretty' applied to the optimized CSS string, instead of calling '.trim()' on the optimized CSS string.

*   The existing 'compileCss' function in 'packages/tailwindcss/src/test-utils/run.ts' must be updated to return the output of 'pretty' applied to the optimized CSS string, instead of calling '.trim()' on the optimized CSS string.

*   The existing 'optimizeCss' function in 'packages/tailwindcss/src/test-utils/run.ts' must be updated to return the output of 'pretty' applied to the optimized CSS string, instead of returning the raw optimized CSS string.

*   When given non-empty CSS input, 'pretty' must produce a string whose first character is a newline and whose last character is a newline, with the trimmed CSS content in between.

*   When given an empty string or a whitespace-only string, 'pretty' must return exactly an empty string ('').


*   Interface details: Type: Function
Name: pretty
Location: packages/tailwindcss/src/test-utils/run.ts
Signature: pretty(input: string): string
Description: Formats a CSS string for snapshot comparison. Trims the input; if the trimmed result is empty, returns ''. Otherwise, returns '\n' + trimmedInput + '\n'. Must be exported so tests can import it directly.

Type: Function
Name: run
Location: packages/tailwindcss/src/test-utils/run.ts
Signature: run(candidates: string[]): Promise<string>
Description: Compiles '@tailwind utilities;' with the given candidates and returns pretty(optimize(build(candidates)).code). Previously returned optimize(build(candidates)).code.trim(). Must be updated to use pretty instead of .trim().

Type: Function
Name: compileCss
Location: packages/tailwindcss/src/test-utils/run.ts
Signature: compileCss(css: string, candidates?: string[], options?: Parameters<typeof compile>[1]): Promise<string>
Description: Compiles arbitrary CSS with the given candidates and options, and returns pretty(optimize(build(candidates)).code). Previously returned optimize(build(candidates)).code.trim(). Must be updated to use pretty instead of .trim().

Type: Function
Name: optimizeCss
Location: packages/tailwindcss/src/test-utils/run.ts
Signature: optimizeCss(input: string): string
Description: Runs Lightning CSS optimization on the input string and returns pretty(optimize(input).code). Previously returned optimize(input).code (without pretty formatting). Must be updated to use pretty.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.