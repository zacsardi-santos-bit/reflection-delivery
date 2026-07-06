I'm noticing that when my workspace configuration file gets updated programmatically, the formatting of the file changes in unexpected ways.

*   When updateWorkspaceManifest inserts a new top-level field into a manifest where blank lines exist between existing top-level sections, it must insert blank lines around the new section to match the existing document style.

*   When updateWorkspaceManifest appends a new top-level field at the end of a manifest (because the existing layout is unordered) and blank lines exist between existing sections, it must add a blank line before the newly appended section.

*   When updateWorkspaceManifest inserts a new top-level field that sorts alphabetically before all existing keys, blank lines from the original document must be preserved around the inserted section.

*   When the original manifest has no blank lines between top-level sections, updateWorkspaceManifest must not add any blank lines when inserting or appending new top-level fields.

*   When all existing top-level keys in the manifest are in alphabetical order — even when the 'packages' key is not first — updateWorkspaceManifest must insert new top-level keys in their alphabetically sorted position rather than appending them.

*   When updatedOverrides adds a new key and the existing override keys are alphabetically sorted, the new key must be inserted at its correct sorted position within the overrides section.

*   When updatedFields updates values for existing keys in a section that contains unsorted keys, the existing key order must be preserved; any new keys not present in the original section must be appended at the end.


*   Interface details: Type: Function
Name: updateWorkspaceManifest
Location: workspace/workspace-manifest-writer/src/index.ts
Signature: updateWorkspaceManifest(dir: string, options: { updatedFields?: Record<string, Record<string, unknown>>, updatedOverrides?: Record<string, string> }): Promise<void>
Description: Updates the pnpm-workspace.yaml file in the given directory by merging the provided field updates. When inserting or appending new top-level fields, the function must preserve the blank-line style of the original document. When existing top-level keys are in alphabetical order, new keys are inserted in their sorted position; otherwise they are appended at the end. Within a section, if the existing keys are alphabetically sorted, new keys are inserted in sorted position; if unsorted, existing key order is preserved and new keys are appended at the end.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.