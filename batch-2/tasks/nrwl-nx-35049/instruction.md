I'm working on deprecating a Tailwind CSS glob-pattern utility in the Angular plugin.

*   The createGlobPatternsForDependencies function exported from packages/angular/tailwind.ts must call console.warn with a message containing the string '"@nx/angular/tailwind" is deprecated' the first time the function is invoked.

*   The deprecation warning must be emitted at most once per module load (process), regardless of how many times createGlobPatternsForDependencies is called — calling it multiple times must still result in exactly one deprecation warning.

*   The function must accept at least one parameter (a directory path string) and continue to return its normal output (glob patterns or an empty array on error) after emitting the warning.


*   Interface details: Type: Function
Name: createGlobPatternsForDependencies
Location: packages/angular/tailwind.ts
Signature: createGlobPatternsForDependencies(dirPath: string, fileGlobPattern?: string) -> string[]
Description: Exported function that generates glob patterns for Tailwind CSS content configuration based on a project's dependencies. Must emit a console.warn deprecation message containing the exact string '"@nx/angular/tailwind" is deprecated' the first time it is called. Subsequent calls in the same process must NOT emit the deprecation warning again. On error resolving patterns, returns an empty array.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.