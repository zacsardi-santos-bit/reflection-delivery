I'm working on a project that uses a lot of side-effect imports — polyfills, CSS files, and initialization modules that don't export anything.

*   The organizeImports action must support a 'kind' property in import group configuration entries. A value of 'bare' matches side-effect-only imports (those without any import bindings, e.g., `import "polyfill"`). A value of '!bare' matches imports that have at least one binding (default, named, or namespace imports).

*   When a group entry has both 'kind' and 'source' properties, it must match only imports that satisfy both conditions: bare imports whose module specifier matches the given glob pattern (e.g., { "kind": "bare", "source": "**/*.css" } matches only bare imports from CSS files).

*   The organizeImports options must support a boolean 'sortBareImports' field. When set to true, bare imports within any group are sorted alphabetically by their module specifier.

*   The 'kind' group specifier must work correctly alongside existing predefined group tokens such as the node built-ins group token and blank line separator tokens.

*   When using a group with 'kind': 'bare' and sortBareImports is true, all matching bare imports must be sorted alphabetically. For example, bare imports with specifiers 'aaa-polyfill', 'mmm-polyfill', and 'zzz-polyfill' must appear in that alphabetical order.

*   When using 'kind': '!bare' and 'kind': 'bare' groups together with a blank line separator, non-bare imports must be placed in the '!bare' group and bare imports in the 'bare' group, separated by a blank line. The diagnostic message must report 'The imports and exports are not sorted.' and offer a safe fix labeled 'Organize Imports (Biome)'.

*   When a source glob is used to match a subset of bare imports (e.g., CSS files), those matching bare imports must appear in the first group sorted alphabetically, and the remaining bare imports must fall into a subsequent 'kind': 'bare' group and be sorted alphabetically as well.

*   The configuration schema at packages/@biomejs/biome/configuration_schema.json must be updated to allow the 'kind' and 'sortBareImports' options in the organizeImports options object.


*   Interface details: The implementation requires changes to Biome's `organizeImports` action configuration and import grouping logic. The tests do not import specific functions by name — they operate via configuration-driven snapshot tests. However, the following configuration schema fields and behavior must be implemented:

## Configuration Schema Changes

**File:** `packages/@biomejs/biome/configuration_schema.json`

The `organizeImports` options object must be extended to support:

1. **`sortBareImports`** (boolean): When `true`, sorts bare imports (side-effect-only imports with no bindings) alphabetically by their module specifier within their assigned group.

2. **`groups` array entries with `kind` property**: Group entries in the `groups` array must support an object shape with:
   - `"kind": "bare"` — matches imports that have no binding specifiers (e.g., `import "foo"`)
   - `"kind": "!bare"` — matches imports that have at least one binding specifier (e.g., `import x from "y"`, `import { a } from "b"`)
   - Combined with an optional `"source"` glob string to further filter by the module specifier path (e.g., `{ "kind": "bare", "source": "**/*.css" }`)

## Existing Configuration Context

The options are configured under:
```
assist.actions.source.organizeImports.options
```

The existing predefined group tokens such as `[":NODE:"]` and separator tokens such as `":BLANK_LINE:"` continue to work alongside the new `kind`-based group entries.

## Test Snapshot Files

The following snapshot files define the exact expected diagnostic output and fix diffs that the implementation must produce. These files are the source of truth for what the fixed import ordering must look like:

- `crates/biome_js_analyze/tests/specs/source/organizeImports/bare-grouping.js.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/bare-grouping-only.js.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/bare-grouping-with-predefined.js.snap`
- `crates/biome_js_analyze/tests/specs/source/organizeImports/bare-grouping-with-source.js.snap`

Each snapshot expects the diagnostic message: `The imports and exports are not sorted.` with a safe fix labeled `Organize Imports (Biome)`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.