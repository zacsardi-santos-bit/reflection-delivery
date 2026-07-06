I'm working on improving the git version control strategy in Docusaurus.

*   The createVcsGitEagerConfig function must be exported as a named export from packages/docusaurus-utils/src/vcs/vcsGitEager.ts and must act as a factory — each call returns a new, independent VcsConfig instance with its own state (not a shared singleton).

*   The VcsConfig instance returned by createVcsGitEagerConfig must expose an initialize({siteDir: string}) method that accepts the site directory path and triggers eager loading of git file metadata for that directory.

*   The returned config's getFileLastUpdateInfo(filepath) method must resolve with an object of shape {author: string, timestamp: number} where timestamp is the Unix timestamp in milliseconds of the most recent git commit for that file.

*   The returned config's getFileCreationInfo(filepath) method must resolve with an object of shape {author: string, timestamp: number} where timestamp is the Unix timestamp in milliseconds of the earliest (creation) git commit for that file.

*   Both getFileLastUpdateInfo and getFileCreationInfo must correctly handle files that reside inside git submodules, returning commit information sourced from the submodule's own git history.

*   When initialize is called with a siteDir that is not inside any git worktree, subsequent calls to getFileLastUpdateInfo must reject with an error whose message is exactly two lines: the first line is 'This Docusaurus site is outside any Git worktree.' and the second line is 'Unable to read Git info for file "<filepath>" ' where <filepath> is the absolute path of the requested file, the path is surrounded by double-quote characters, and there is a single trailing space after the closing double quote.


*   Interface details: Type: Function
Name: createVcsGitEagerConfig
Location: packages/docusaurus-utils/src/vcs/vcsGitEager.ts
Signature: createVcsGitEagerConfig() -> VcsConfig
Description: Factory function that creates and returns a new VcsConfig instance implementing the Git eager loading strategy. Each call produces an independent instance (not a singleton). The returned object exposes three members: initialize({siteDir: string}), getFileLastUpdateInfo(filepath: string) -> Promise<{author: string, timestamp: number} | null>, and getFileCreationInfo(filepath: string) -> Promise<{author: string, timestamp: number} | null>.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.