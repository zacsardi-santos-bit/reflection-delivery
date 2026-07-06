I'm working on adding a codemod package to the Payload CMS monorepo.

*   The parseFlags function must accept an array of CLI argument strings and return an object with exactly the fields: dry (boolean), list (boolean), path (string), print (boolean), and transform (string or undefined).

*   When parseFlags is called with an empty array, it must return { dry: false, list: false, path: process.cwd(), print: false, transform: undefined }.

*   When parseFlags receives a non-flag string as the first positional argument (e.g. './src'), it must set the path field to that string value.

*   parseFlags must set dry=true when --dry is present, set list=true when --list is present, and set print=true when --print is present.

*   parseFlags must treat --dry-run as an alias for --dry, setting dry=true.

*   parseFlags must accept --transform followed by a value and set the transform field to that value (e.g. --transform rename-slate-export sets transform to 'rename-slate-export').

*   The runTransforms function must accept an object with a ts-morph Project and an array of Transform objects, apply each transform in order, and return a Promise resolving to an object with failed (boolean) and results (array).

*   Each entry in the results array returned by runTransforms must have: name (the transform's name string), filesChanged (array of file path strings), optional notes (array of strings), and optional error (Error instance if the transform threw).

*   runTransforms must continue executing remaining transforms even when a transform throws an error — all transforms must run regardless of earlier failures.

*   runTransforms must set failed=true if any transform threw an error, and failed=false if all transforms completed without throwing.

*   The Transform interface in types.ts must define: name (string), description (string), and apply function that receives a context object containing a ts-morph Project (i.e. { project: Project }) and returns an object with filesChanged (string array) and optional notes (string array).

*   The exampleNoop transform must implement the Transform interface and must leave source files unchanged — when applied to basic.input.ts, the result must equal basic.output.ts exactly, and running it again on the output must produce the same output (idempotent).

*   The runTransform test helper must accept { source: string, transform: Transform }, create an in-memory ts-morph Project containing the source string as a TypeScript file, apply the transform by calling transform.apply({ project }), and return the resulting file's source text as a string.


*   Interface details: Type: Function
Name: parseFlags
Location: packages/codemod/src/cli.parseFlags.ts
Signature: parseFlags(argv: string[]) -> { dry: boolean, list: boolean, path: string, print: boolean, transform: string | undefined }
Description: Parses a CLI argument array and returns a structured flags object. The first positional (non-flag) argument is used as the path. Recognizes --dry, --dry-run (alias for --dry), --list, --print, and --transform <value> flags. Defaults: dry=false, list=false, path=process.cwd(), print=false, transform=undefined.

Type: Function
Name: runTransforms
Location: packages/codemod/src/runner.ts
Signature: runTransforms({ project: Project, transforms: Transform[] }) -> Promise<{ failed: boolean, results: Array<{ name: string, filesChanged: string[], notes?: string[], error?: Error }> }>
Description: Runs all supplied transforms in order against a ts-morph Project. Continues past any transform that throws, collecting results from all transforms. Returns failed=true if any transform threw an error. Each result entry contains the transform name, the list of changed files (or empty array), optional notes, and an optional error if the transform threw. Each transform is called with a context object of the form { project }.

Type: Interface (TypeScript type alias)
Name: Transform
Location: packages/codemod/src/types.ts
Description: Describes a single code transform that can be applied to a ts-morph Project. The apply function receives a TransformContext object (with a project property), not the Project directly.
Fields:
  name: string
  description: string
  apply: (ctx: { project: Project }) => Promise<{ filesChanged: string[], notes?: string[] }> | { filesChanged: string[], notes?: string[] }

Type: Constant (Transform object)
Name: exampleNoop
Location: packages/codemod/src/transforms/example-noop/index.ts
Description: A no-op example Transform that satisfies the Transform interface and leaves source files unchanged. Requires fixture files basic.input.ts and basic.output.ts in the same directory (packages/codemod/src/transforms/example-noop/). The transform produces output identical to basic.output.ts when run on basic.input.ts, and running it again on the output produces the same result (idempotent).

Type: Function
Name: runTransform
Location: packages/codemod/src/utils/test-helpers.ts
Signature: runTransform({ source: string, transform: Transform, filename?: string }) -> Promise<string>
Description: Test helper that creates an in-memory ts-morph Project (useInMemoryFileSystem: true) containing the source string as a single TypeScript file (default filename 'input.ts'), applies the transform by calling transform.apply({ project }), and returns the resulting file's source text as a string.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.