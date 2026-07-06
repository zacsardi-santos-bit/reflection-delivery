I'm working on the OpenLineage Python client, which has a feature that automatically attaches git metadata (repository URL, current commit hash, branch, and tag) to lineage events as a source code location facet.

*   A new module must exist at `client/python/openlineage/client/git.py` and export these symbols: `_find_git_dir`, `_find_tag_in_packed_refs`, `_read_head_content`, `_read_head_sha`, `_resolve_ref`, `get_git_branch`, `get_git_repo_url`, `get_git_tag`, `get_git_version`.

*   The function `_find_git_dir(path: str) -> Path | None` must traverse from `path` upward through parent directories looking for a `.git` entry. If found as a directory, return it as a `Path`. If `.git` is a file containing a `gitdir: <pointer>` line, resolve the pointer (absolute or relative to the `.git` file's parent) and return that `Path`. Return `None` if no `.git` is found anywhere in the hierarchy.

*   The function `_read_head_content(git_dir: Path) -> str | None` must read `git_dir / 'HEAD'`, strip trailing whitespace, and return the result. Return `None` if the file does not exist.

*   The function `_read_head_sha(git_dir: Path) -> str | None` must return the commit SHA currently pointed to by HEAD. When HEAD contains a raw SHA (detached HEAD), return that SHA. When HEAD is a symbolic ref (`ref: refs/heads/<branch>`), resolve the ref via `_resolve_ref` and return the SHA. Return `None` when HEAD does not exist.

*   The function `_resolve_ref(git_dir: Path, ref: str) -> str | None` must look up `ref` first as a loose file at `git_dir / ref` and return its stripped content. If not found, search `git_dir / 'packed-refs'` for a line matching `<sha> <ref>` and return the SHA. Return `None` if the ref is not found in either location.

*   The function `_find_tag_in_packed_refs(git_dir: Path, sha: str) -> str | None` must search `git_dir / 'packed-refs'` for a tag that matches `sha`. For lightweight tags, the SHA appears directly on the `<sha> refs/tags/<name>` line. For annotated tags, the SHA appears on a `^<sha>` dereference line immediately after the tag entry. Return the tag name (the part after `refs/tags/`). Return `None` if `packed-refs` does not exist, if the SHA is not associated with any tag, or if the matching ref is not under `refs/tags/`.

*   The function `get_git_version(git_dir: Path) -> str | None` must return the current commit SHA by delegating to `_read_head_sha`. Return `None` when HEAD is missing.

*   The function `get_git_branch(git_dir: Path) -> str | None` must return the branch name by reading a symbolic ref from HEAD and stripping the `refs/heads/` prefix. Return `None` when HEAD is in detached state (contains a raw SHA) or when HEAD does not exist.

*   The function `get_git_tag(git_dir: Path) -> str | None` must first obtain the current commit SHA. Then search `git_dir / 'refs' / 'tags'` recursively for a loose tag file whose content matches that SHA, returning the relative path from `refs/tags/` as the tag name. If not found in loose files, fall back to `_find_tag_in_packed_refs`. Return `None` when no matching tag is found or when HEAD is missing.

*   The function `get_git_repo_url(repo_url: str | None = None, git_dir: Path | None = None) -> str | None` must return `repo_url` as-is when it is a non-empty string. When `repo_url` is `None` or empty, read the origin remote URL from `git_dir / 'config'` using a raw (non-interpolating) config parser so that URLs containing percent signs do not raise errors. Return `None` when the config file is missing, when no `origin` remote is present, or when no git dir is available.

*   The `SourceCodeLocationConfig` default configuration must have `disabled` set to `True` (the source code location facet is disabled by default).

*   The `_source_code_location` property on `OpenLineageClient` must be cached so that repeated accesses return the same object instance without re-computing git information.

*   The `openlineage.client.client` module must import `_find_git_dir` from `openlineage.client.git` so it can be patched at `openlineage.client.client._find_git_dir` during tests.


*   Interface details: Type: Module
Name: git
Location: client/python/openlineage/client/git.py
Description: New module providing pure-Python git metadata detection by reading git's internal filesystem data structures directly, without subprocess calls.

---

Type: Function
Name: _find_git_dir
Location: client/python/openlineage/client/git.py
Signature: _find_git_dir(path: str) -> Path | None
Description: Traverses upward from `path` to find the `.git` directory. If `.git` is a file (linked worktree), reads the `gitdir:` pointer and resolves it (absolute or relative). Returns a `Path` to the actual git directory, or `None` if not found.

---

Type: Function
Name: _read_head_content
Location: client/python/openlineage/client/git.py
Signature: _read_head_content(git_dir: Path) -> str | None
Description: Reads and returns the content of `git_dir/HEAD` with trailing whitespace stripped. Returns `None` if HEAD does not exist.

---

Type: Function
Name: _read_head_sha
Location: client/python/openlineage/client/git.py
Signature: _read_head_sha(git_dir: Path) -> str | None
Description: Returns the commit SHA currently pointed to by HEAD. Handles both detached HEAD (raw SHA) and symbolic refs (`ref: refs/heads/<branch>`) by delegating to `_resolve_ref`. Returns `None` if HEAD is missing.

---

Type: Function
Name: _resolve_ref
Location: client/python/openlineage/client/git.py
Signature: _resolve_ref(git_dir: Path, ref: str) -> str | None
Description: Resolves a git ref (e.g. `refs/heads/main`) to a commit SHA. Checks the loose file at `git_dir/ref` first, then falls back to searching `git_dir/packed-refs`. Returns `None` if not found.

---

Type: Function
Name: _find_tag_in_packed_refs
Location: client/python/openlineage/client/git.py
Signature: _find_tag_in_packed_refs(git_dir: Path, sha: str) -> str | None
Description: Searches `git_dir/packed-refs` for a tag whose commit matches `sha`. Handles lightweight tags (SHA on the tag line) and annotated tags (SHA on the following `^<sha>` dereference line). Returns the tag name (path after `refs/tags/`), or `None` if not found or `packed-refs` is absent.

---

Type: Function
Name: get_git_version
Location: client/python/openlineage/client/git.py
Signature: get_git_version(git_dir: Path) -> str | None
Description: Returns the current commit SHA from `git_dir`. Returns `None` if HEAD is missing.

---

Type: Function
Name: get_git_branch
Location: client/python/openlineage/client/git.py
Signature: get_git_branch(git_dir: Path) -> str | None
Description: Returns the current branch name by parsing a symbolic ref from HEAD (strips `refs/heads/` prefix). Returns `None` when in detached HEAD state or when HEAD does not exist.

---

Type: Function
Name: get_git_tag
Location: client/python/openlineage/client/git.py
Signature: get_git_tag(git_dir: Path) -> str | None
Description: Returns the tag name pointing at the current commit. Searches loose tag files in `git_dir/refs/tags/` recursively first, then falls back to `_find_tag_in_packed_refs`. Returns the tag name relative to `refs/tags/`. Returns `None` if no matching tag is found or HEAD is missing.

---

Type: Function
Name: get_git_repo_url
Location: client/python/openlineage/client/git.py
Signature: get_git_repo_url(repo_url: str | None = None, git_dir: Path | None = None) -> str | None
Description: Returns the repository URL. If `repo_url` is a non-empty string, returns it as-is. Otherwise reads the origin remote URL from `git_dir/config` using a non-interpolating config parser (to handle percent-encoded URLs). Returns `None` if no URL is available, config is missing, or no origin remote exists.

---

Note: `openlineage.client.client` must import `_find_git_dir` from `openlineage.client.git` so it is accessible as `openlineage.client.client._find_git_dir` (the tests patch it at this path).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.