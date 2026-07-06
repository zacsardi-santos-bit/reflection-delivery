I'd like to reorganize some evaluation utility functions in our codebase.

*   The evalTableUtils module must be relocated to src/util/eval/evalTableUtils.ts and must export: evalTableToCsv, evalTableToJson, getEvalTableOutputPromptLocationsBySize, getEvalTablePromptStrippedPayload, STRIPPED_TABLE_CELL_PROMPT, streamEvalCsv, and mergeComparisonTables.

*   The existing module at src/server/utils/evalTableUtils must re-export evalTableToCsv, streamEvalCsv, and mergeComparisonTables from src/util/eval/evalTableUtils as the exact same function references (not copies), so that the old import path remains backward compatible.

*   The filterTests function must be available at src/util/eval/filterTests instead of src/commands/eval/filterTests.

*   The filterPrompts function must be available at src/util/eval/filterPrompts instead of src/commands/eval/filterPrompts.

*   The filterProviderConfigs and filterProviders functions must be available at src/util/eval/filterProviders instead of src/commands/eval/filterProviders.

*   The filterTestsByResults function must be available at src/util/eval/filterTestsUtil instead of src/commands/eval/filterTestsUtil.


*   Interface details: Type: Module (new location)
Name: evalTableUtils
Location: src/util/eval/evalTableUtils.ts
Description: Eval table utility module moved from src/server/utils/evalTableUtils.ts to a shared utility layer. Must export all of the following symbols.

Exports required:
- evalTableToCsv (function)
- evalTableToJson (function)
- getEvalTableOutputPromptLocationsBySize (function)
- getEvalTablePromptStrippedPayload (function)
- STRIPPED_TABLE_CELL_PROMPT (constant string)
- streamEvalCsv (function)
- mergeComparisonTables (function)

---

Type: Module (backward-compat re-export shim)
Name: evalTableUtils (legacy path)
Location: src/server/utils/evalTableUtils.ts
Description: Must re-export evalTableToCsv, streamEvalCsv, and mergeComparisonTables from src/util/eval/evalTableUtils as the exact same references. Consumers importing from this path must receive the same function objects as consumers importing from the new path.

---

Type: Module (new location)
Name: filterTests
Location: src/util/eval/filterTests.ts
Description: Must export the filterTests function. Previously located at src/commands/eval/filterTests.ts.

---

Type: Module (new location)
Name: filterPrompts
Location: src/util/eval/filterPrompts.ts
Description: Must export the filterPrompts function. Previously located at src/commands/eval/filterPrompts.ts.

---

Type: Module (new location)
Name: filterProviders
Location: src/util/eval/filterProviders.ts
Description: Must export filterProviderConfigs and filterProviders functions. Previously located at src/commands/eval/filterProviders.ts.

---

Type: Module (new location)
Name: filterTestsUtil
Location: src/util/eval/filterTestsUtil.ts
Description: Must export the filterTestsByResults function. Previously located at src/commands/eval/filterTestsUtil.ts.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.