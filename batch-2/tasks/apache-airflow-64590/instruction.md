I'm working on the Airflow development tooling and we need to add a persistent caching layer for pull request data that gets fetched from GitHub.

*   The file dev/breeze/src/airflow_breeze/utils/pr_cache.py must be created and export: CacheStore, author_cache, review_cache, triage_cache, save_author_profile, get_cached_author_profile, save_review_cache, scan_cached_pr_numbers, and invalidate_stale_caches.

*   CacheStore must be a class with a _cache_name string attribute, a _ttl_seconds integer attribute, a cache_dir(github_repository) method that returns a directory path, and a save(github_repository, key, data) method that writes data as JSON to a file named <key>.json inside cache_dir(github_repository).

*   author_cache must be a module-level CacheStore instance whose _ttl_seconds attribute equals exactly 7 * 24 * 3600 (604800).

*   save_author_profile(github_repository, username, profile) must persist the profile dict to a file named author_<username>.json in author_cache.cache_dir(github_repository), storing a cached_at field containing the current Unix timestamp alongside the profile data.

*   get_cached_author_profile(github_repository, username) must return the stored profile dict when the file exists and cached_at is within 7 * 24 * 3600 seconds of the current time. It must return None when the file does not exist or when cached_at is more than 7 * 24 * 3600 seconds in the past.

*   save_review_cache(github_repository, pr_number, sha, data) must save the SHA and the data dict to a file named pr_<pr_number>.json in review_cache.cache_dir(github_repository).

*   scan_cached_pr_numbers(github_repository) must return a dict mapping integer PR numbers to a dict containing at minimum a 'review_cache' key holding the stored SHA (e.g. {100: {'review_cache': 'sha_aaa'}}). It must scan only files matching the pattern pr_<number>.json, silently skip files with other names, silently skip files with corrupt or unparseable JSON, and return {} when no matching files are found.

*   invalidate_stale_caches(github_repository, pr_sha_mapping) must accept a dict of {pr_number: current_sha}, remove files from both review_cache and triage_cache where the stored SHA does not match current_sha, and return the total count of deleted files. Corrupt files that cannot be parsed must also be deleted and counted. When the stored SHA matches or no file exists, no deletion occurs and 0 is contributed to the count.

*   The file dev/breeze/src/airflow_breeze/utils/pr_vault.py must be created and export: save_pr, load_pr, save_prs_batch, save_check_status, load_check_status, save_workflow_runs, load_workflow_runs, and generate_review_questions.

*   save_pr(github_repository, pr_object) must serialize the following PR object attributes to a file named pr_<number>.json: number, title, body, url, created_at, updated_at, node_id, author_login, author_association, head_sha, base_ref, check_summary, checks_state, failed_checks, commits_behind, is_draft, mergeable, labels. The file must also include a cached_at field with the current Unix timestamp.

*   load_pr(github_repository, pr_number, head_sha=None) must return the stored PR dict (without cached_at or unresolved_threads keys) when the file exists, cached_at is within 4 hours (14400 seconds), and — if head_sha is provided — head_sha matches the stored value. Must return None if the file does not exist, if head_sha is provided and does not match, or if cached_at is more than 14400 seconds old.

*   save_prs_batch(github_repository, pr_list) must call save_pr for each item and return the number of PRs saved.

*   save_check_status(github_repository, sha, counts) and load_check_status(github_repository, sha) must persist and retrieve a counts dict keyed by SHA. load_check_status must return None if the SHA is not found or does not match. There is no TTL — the same SHA must return the same result regardless of the age of the cached entry.

*   save_workflow_runs(github_repository, sha, status, runs) must write the runs list to a file named wf_<sha>_<status>.json with a cached_at timestamp. load_workflow_runs(github_repository, sha, status) must return None if the file does not exist, if the status does not match, or if cached_at is more than 600 seconds old.

*   generate_review_questions(diff, body) must return an empty list when diff is empty. For non-empty diffs it returns a list of strings. A string containing 'LARGE PR' must be included when the diff contains 500 or more added lines (lines starting with '+'). A string containing 'TEST COVERAGE' must be included when no file path in the diff points to a test file, and must NOT be included when test files are present. A string containing 'VERSION CHECK' must be included when the diff adds a line containing 'version_added:'. A string containing 'BREAKING CHANGE' must be included when the diff adds a line (prefixed with '+') containing 'BREAKING CHANGE' or 'deprecated:', but must NOT be included when such lines are only removed (prefixed with '-'). A string containing 'CONSISTENCY' must be included when 4 or more exception raises are added. Returns [] when none of these conditions apply.


*   Interface details: Type: Module
Name: pr_cache
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Description: Provides persistent disk-based caching infrastructure for author profiles, review data, and triage data. Exposes module-level CacheStore instances and public helper functions for saving, loading, scanning, and invalidating caches.

Type: Class
Name: CacheStore
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Description: A named cache store backed by a directory on disk. Has a _cache_name attribute (string identifying the store), a _ttl_seconds attribute (int, cache lifetime in seconds), a cache_dir(github_repository) method returning the Path to the cache directory for a given repo, and a save(github_repository, key, data) method that writes data as JSON to <key>.json in cache_dir(github_repository).

Type: Variable
Name: author_cache
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Description: Module-level CacheStore instance for author profiles. author_cache._ttl_seconds must equal 7 * 24 * 3600 (604800).

Type: Variable
Name: review_cache
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Description: Module-level CacheStore instance for PR review data. Exposes cache_dir(github_repository) returning the path to the review cache directory.

Type: Variable
Name: triage_cache
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Description: Module-level CacheStore instance for PR triage data. Exposes save(github_repository, key, data) to persist triage entries.

Type: Function
Name: save_author_profile
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Signature: save_author_profile(github_repository: str, username: str, profile: dict) -> None
Description: Persists the author profile dict to author_<username>.json in author_cache.cache_dir(github_repository), adding a cached_at field with the current Unix timestamp.

Type: Function
Name: get_cached_author_profile
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Signature: get_cached_author_profile(github_repository: str, username: str) -> dict | None
Description: Returns the stored author profile dict if the file exists and cached_at is within 7 days. Returns None if the file does not exist or if cached_at is more than 7 * 24 * 3600 seconds in the past.

Type: Function
Name: save_review_cache
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Signature: save_review_cache(github_repository: str, pr_number: int, sha: str, data: dict) -> None
Description: Saves the SHA and data to pr_<pr_number>.json in review_cache.cache_dir(github_repository).

Type: Function
Name: scan_cached_pr_numbers
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Signature: scan_cached_pr_numbers(github_repository: str) -> dict
Description: Returns a dict mapping integer PR numbers to cache metadata. Scans pr_<number>.json files in the review cache directory. The result has the shape {pr_number: {"review_cache": sha}}. Files not matching the pr_<number>.json pattern are ignored. Corrupt/unreadable JSON files are skipped. Returns {} if no matching entries are found.

Type: Function
Name: invalidate_stale_caches
Location: dev/breeze/src/airflow_breeze/utils/pr_cache.py
Signature: invalidate_stale_caches(github_repository: str, pr_sha_mapping: dict) -> int
Description: Accepts a dict mapping PR numbers to their current SHAs. Removes cache files from review_cache and triage_cache where the stored SHA differs from the current SHA. Corrupt files are also deleted and counted. Returns the total number of files removed across all caches. Returns 0 for PRs whose SHAs match or that have no cache entry.

Type: Module
Name: pr_vault
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Description: Provides save/load functions for PR objects, CI check statuses, workflow runs, and a diff-analysis utility for generating review questions.

Type: Function
Name: save_pr
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: save_pr(github_repository: str, pr_object) -> None
Description: Serializes the PR object's attributes (number, title, body, url, created_at, updated_at, node_id, author_login, author_association, head_sha, base_ref, check_summary, checks_state, failed_checks, commits_behind, is_draft, mergeable, labels) to pr_<number>.json, including a cached_at field with the current Unix timestamp.

Type: Function
Name: load_pr
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: load_pr(github_repository: str, pr_number: int, head_sha: str | None = None) -> dict | None
Description: Returns the stored PR dict. Returns None if the file does not exist, if head_sha is provided and does not match the stored head_sha, or if cached_at is more than 4 hours (14400 seconds) old. The returned dict must NOT contain cached_at or unresolved_threads keys.

Type: Function
Name: save_prs_batch
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: save_prs_batch(github_repository: str, pr_list: list) -> int
Description: Calls save_pr for each PR in the list. Returns the count of PRs saved.

Type: Function
Name: save_check_status
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: save_check_status(github_repository: str, sha: str, counts: dict) -> None
Description: Persists the check status counts dict for the given commit SHA.

Type: Function
Name: load_check_status
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: load_check_status(github_repository: str, sha: str) -> dict | None
Description: Returns the stored counts dict for the given SHA. Returns None if not found or if the SHA does not match. There is no TTL — for a given SHA the same result is returned regardless of how old it is.

Type: Function
Name: save_workflow_runs
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: save_workflow_runs(github_repository: str, sha: str, status: str, runs: list) -> None
Description: Writes the workflow runs list to a file named wf_<sha>_<status>.json, including a cached_at timestamp.

Type: Function
Name: load_workflow_runs
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: load_workflow_runs(github_repository: str, sha: str, status: str) -> list | None
Description: Returns the stored runs list. Returns None if not found, if the status does not match, or if cached_at is more than 600 seconds old.

Type: Function
Name: generate_review_questions
Location: dev/breeze/src/airflow_breeze/utils/pr_vault.py
Signature: generate_review_questions(diff: str, body: str) -> list[str]
Description: Analyzes a PR diff string and returns a list of review concern strings. Returns [] for empty diffs and for small clean PRs (few added lines, test files present, no special markers). The following strings must appear in the list when triggered: one containing "LARGE PR" when the diff has 500 or more added lines; one containing "TEST COVERAGE" when no test file paths appear in the diff (and NOT included when test files are present); one containing "VERSION CHECK" when the diff adds a line with version_added:; one containing "BREAKING CHANGE" when the diff adds a line containing "BREAKING CHANGE" or "deprecated:" (but NOT when such lines are only removed, i.e. prefixed with "-"); one containing "CONSISTENCY" when 4 or more exception raises are added. Returns [] when none of these conditions are met.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.