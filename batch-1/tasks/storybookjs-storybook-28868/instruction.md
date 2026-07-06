Refactor the story file transformation logic in the Storybook vitest addon by moving it into the shared core CSF tooling package. Implement the necessary changes to ensure the transformation logic is centralized, reusable, and maintains the existing functionality while addressing usability issues.

*   Implement `vitestTransform` function in `code/core/src/csf-tools/vitest-plugin/transformer.ts`:
    *   Accept an object with fields: `code`, `fileName`, `configDir`, `tagsFilter`, and `stories`.
    *   Return a plain string for non-story files or an object with `code` and `map` for story files.
    *   Use `loadCsf` with `transformInlineMeta:true` and call `parse()` for story files.
    *   Generate unique identifier aliases using Babel's `scope.generateUidIdentifier`.
    *   Prepend import declarations in this order: 
        1. `'import { test as _test } from "vitest";'`
        2. `'import { composeStory as _composeStory } from "storybook/internal/preview-api";'`
        3. `'import { testStory as _testStory, isValidTest as _isValidTest } from "@storybook/experimental-addon-vitest/internal/test-utils";'`
    *   Handle inline and const-declared default exports, adding titles when missing.
    *   Append test wrapper code for named story exports, respecting tag filters and exclusions.
    *   Generate accurate source maps linking test failures to original story file lines.

*   Update `CsfFile` class in `code/core/src/csf-tools/CsfFile.ts`:
    *   Expose `_storyStatements`, `_metaVariableName`, and `_file` fields.
    *   Implement `parse()` to populate `_storyStatements` and manage `_metaVariableName`.
    *   Use `babelParseFile` to create `_file`.

*   Modify `formatCsf` function in `code/core/src/csf-tools/CsfFile.ts`:
    *   Accept optional arguments for code generation options and original source code.
    *   Return an object with `code` and `map` when `sourceMaps:true` is provided.

*   Export `CsfOptions` interface from `code/core/src/csf-tools/CsfFile.ts`:
    *   Include `transformInlineMeta` as an optional boolean property.

*   Update `isValidTest` function in `code/addons/vitest/src/plugin/test-utils.ts`:
    *   Export by name and replace the previous `shouldRun` export.
    *   Check inclusion and exclusion criteria based on `tagsFilter`.

*   Modify `testStory` function in `code/addons/vitest/src/plugin/test-utils.ts`:
    *   Change signature to accept `(Story: ComposedStoryFn, tagsFilter)`.
    *   Skip test if any skip tag appears in `Story.tags`.
    *   Set `task.meta.storyId = Story.id` and call `Story.run()`.

*   Ensure the debug URL in error messages always includes `&addonPanel=storybook/interactions/panel`.

*   Update `storybookTest` plugin in `code/addons/vitest/src/plugin/index.ts`:
    *   Import `vitestTransform` from `'storybook/internal/csf-tools'`.
    *   Call `vitestTransform` with appropriate parameters and default tag values using nullish coalescing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.