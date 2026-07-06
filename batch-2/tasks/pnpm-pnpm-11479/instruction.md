I'm working on the release tooling for a large monorepo and I need to add a utility module that handles changeset bookkeeping during releases.

*   The branchToFilename function must convert any branch name to a filename by replacing every forward-slash character with a dash and appending '.txt' (e.g., 'main' → 'main.txt', 'release/10.0' → 'release-10.0.txt', 'feature/foo/bar' → 'feature-foo-bar.txt').

*   The readReleased function must return an empty Set when the given directory does not exist, without throwing.

*   The readReleased function must read all '.txt' files in the directory and return a Set of IDs collected from their lines, merging across multiple files and deduplicating. Lines beginning with '#' and lines that are empty or contain only whitespace must be skipped. Files with any extension other than '.txt' (e.g., '.md') must be ignored.

*   The appendReleased function must be a no-op when the ids array is empty — it must not create any file.

*   The appendReleased function must write IDs to the file named by branchToFilename(branch) inside the given directory, deduplicating against IDs already present in that file, sorting all IDs alphabetically, and writing them one per line with a trailing newline.

*   The appendReleased function must use the sanitized (slash-replaced) filename when the branch name contains '/' characters.

*   The appendReleased function must create the target directory (and any intermediate parent directories) if it does not already exist.

*   The hideReleased function must rename each '<id>.md' file in the directory to '<id>.md.released' for every ID in the releasedIds Set that has a corresponding '.md' file. IDs in releasedIds with no matching '.md' file must be silently skipped.

*   The hideReleased function must return an array of objects where each object has at minimum an 'id' string property equal to the basename of the hidden changeset file (without the '.md' extension).

*   The hideReleased function must implement atomic rollback: if any rename operation fails mid-process, all renames already completed must be undone (the '.md.released' files renamed back to '.md') and the original error must be re-thrown.

*   The restoreHidden function must rename each '.md.released' file back to its original '.md' name, given the array returned by hideReleased. After restoration the '.md' file must exist and the '.md.released' file must not.

*   The deleteHidden function must permanently delete each '.md.released' file referenced in the array returned by hideReleased. After deletion neither the '.md' nor the '.md.released' file must exist.

*   The listChangesetIds function must return a sorted array of IDs by listing '.md' files in the given directory, stripping the '.md' extension. It must exclude 'README.md' and must ignore files that do not have the '.md' extension.


*   Interface details: Type: Function
Name: branchToFilename
Location: __utils__/scripts/src/bump.ts
Signature: branchToFilename(branch: string): string
Description: Converts a git branch name to a safe filename by replacing all forward-slash characters with dashes and appending the `.txt` extension. For example, "release/10.0" becomes "release-10.0.txt" and "main" becomes "main.txt".

Type: Function
Name: readReleased
Location: __utils__/scripts/src/bump.ts
Signature: readReleased(dir: string): Set<string>
Description: Reads all `.txt` files in the given directory and collects their non-empty, non-comment lines as changeset IDs. Returns a Set merging IDs across all `.txt` files. Lines starting with `#` and lines that are empty or contain only whitespace are ignored. Non-`.txt` files in the directory are ignored. Returns an empty Set if the directory does not exist.

Type: Function
Name: appendReleased
Location: __utils__/scripts/src/bump.ts
Signature: appendReleased(dir: string, branch: string, ids: string[]): void
Description: Appends the given list of changeset IDs to the file corresponding to `branchToFilename(branch)` inside `dir`. Deduplicates against any IDs already present in that file. Writes the merged, sorted list back to the file (one ID per line, with a trailing newline). Creates `dir` (and any parent directories) if they do not exist. Is a no-op if `ids` is empty — no file is created.

Type: Function
Name: hideReleased
Location: __utils__/scripts/src/bump.ts
Signature: hideReleased(dir: string, releasedIds: Set<string>): Array<{ id: string; [key: string]: unknown }>
Description: For each `.md` file in `dir` whose basename (without `.md`) is contained in `releasedIds`, renames the file from `<id>.md` to `<id>.md.released`. Returns an array of objects, each with at minimum an `id` string property equal to the basename of the hidden file. IDs in `releasedIds` that have no corresponding `.md` file are silently skipped. If any rename fails mid-way through the process, all renames already performed are rolled back (i.e., the `.md.released` files are renamed back to `.md`) and the original error is re-thrown.

Type: Function
Name: restoreHidden
Location: __utils__/scripts/src/bump.ts
Signature: restoreHidden(hidden: Array<{ id: string; [key: string]: unknown }>): void
Description: Renames each `.md.released` file referenced in the `hidden` array back to its original `.md` filename. Accepts the array returned by `hideReleased`.

Type: Function
Name: deleteHidden
Location: __utils__/scripts/src/bump.ts
Signature: deleteHidden(hidden: Array<{ id: string; [key: string]: unknown }>): void
Description: Permanently deletes each `.md.released` file referenced in the `hidden` array. Accepts the array returned by `hideReleased`.

Type: Function
Name: listChangesetIds
Location: __utils__/scripts/src/bump.ts
Signature: listChangesetIds(dir: string): string[]
Description: Returns a sorted array of changeset IDs by listing `.md` files in `dir`, stripping the `.md` extension, and excluding `README`. Non-`.md` files in the directory are ignored.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.