I'm working on a language server that provides code action suggestions backed by a linter.

*   When code actions are requested with an explicitly invoked trigger kind and no cached results exist for the file, the server must automatically lint the file and return the resulting code actions — without requiring a prior explicit lint run.

*   When code actions are requested with a default or unspecified trigger kind and no cached results exist, the server must return an empty list (0 code actions) without running the linter.

*   When code actions are requested with a default or unspecified trigger kind and cached results already exist from a previous lint run, the server must return those cached code actions.

*   A JavaScript file containing a single debugger statement, when linted, must produce exactly 3 code actions: 1 rule-specific fix action and 2 diagnostic suppression (ignore) actions.

*   The fixture file at apps/oxlint/fixtures/lsp/code_action/trigger-kind-invoked.js must be created and contain exactly one debugger statement as its content (i.e., the text 'debugger;').

*   The fixture file at apps/oxlint/fixtures/lsp/code_action/test-baseline.js must exist as an empty JavaScript file in the code_action fixtures directory.


*   Interface details: Type: File
Name: trigger-kind-invoked.js
Location: apps/oxlint/fixtures/lsp/code_action/trigger-kind-invoked.js
Description: A JavaScript fixture file used by the invoked-trigger code action test. Must contain exactly one debugger statement (`debugger;`) as its content so that linting this file produces exactly 3 code actions (1 rule-specific fix + 2 ignore/suppression options).

Type: File
Name: test-baseline.js
Location: apps/oxlint/fixtures/lsp/code_action/test-baseline.js
Description: An empty JavaScript fixture file that ensures the code_action fixtures directory is treated as a valid workspace. No content is required.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.