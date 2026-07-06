Implement a library-level API in the `jj` version control library to allow programmatic file-fixing across commits. This API should enable users to apply custom file transformations, handle commit graph traversal, and rewrite commits as needed. Ensure the API supports parallel processing for improved performance and provides detailed summaries of operations performed.

*   Create a new `fix` module in `lib/src/fix.rs` and publicly export it from the `jj-lib` crate by adding `pub mod fix;` in `lib/src/lib.rs`.

*   Define the `FileToFix` struct:
    *   Fields: `pub repo_path: RepoPathBuf`, `pub file_id: FileId`.
    *   Implement `Hash`, `Eq`, and `PartialEq` for use in `HashSet` and as a `HashMap` key.

*   Define the `FileFixer` trait:
    *   Method: `fix_files<'a>(&self, store: &Store, files_to_fix: &'a HashSet<FileToFix>) -> Result<HashMap<&'a FileToFix, FileId>, FixError>`.
    *   Return a map indicating which files were changed.

*   Define the `FixSummary` struct:
    *   Fields: `pub rewrites: HashMap<CommitId, CommitId>`, `pub num_checked_commits: i32`, `pub num_fixed_commits: i32`.

*   Define the `FixError` enum:
    *   Variant: `FixContent(Box<dyn std::error::Error + Send + Sync>)`.
    *   Implement `Display` to delegate to the inner error's message.
    *   Support backend, revset evaluation, and IO errors.

*   Implement the `fix_files` function:
    *   Signature: `fix_files(root_commits: Vec<CommitId>, matcher: &dyn Matcher, include_unchanged_files: bool, repo: &mut MutableRepo, file_fixer: &impl FileFixer) -> Result<FixSummary, FixError>`.
    *   Traverse commits from `root_commits`, apply `file_fixer` to matching files, and rewrite changed commits.
    *   Return a `FixSummary` of the operations.

*   Implement conditional file processing:
    *   When `include_unchanged_files` is `false`, only pass files differing from the parent commit to the fixer.
    *   When `include_unchanged_files` is `true`, pass all matching files regardless of changes.

*   Handle fixer results:
    *   Exclude unchanged commits from `rewrites` and do not increment `num_fixed_commits`.
    *   Propagate `FixContent` errors immediately, stopping further processing.

*   Ensure commits with no files are processed without error, counting toward `num_checked_commits` but not affecting `rewrites` or `num_fixed_commits`.

*   Define the `ParallelFileFixer` struct:
    *   Implement `FileFixer`.
    *   Constructor: `new(fix_fn: T) -> Self` where `T: Fn(&Store, &FileToFix) -> Result<Option<FileId>, FixError> + Sync + Send`.
    *   Process files concurrently and propagate errors without partial changes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.