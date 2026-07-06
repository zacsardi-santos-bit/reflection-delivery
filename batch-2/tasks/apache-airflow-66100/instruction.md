I'm working on the documentation publishing pipeline for provider releases and I need to add a script that intelligently determines which providers should have their registry entries updated for a given wave release.

*   The derive function must accept include_docs (str), ref (str), and a keyword-only git_runner callable (defaults to the real git runner). It must return a 3-tuple: (registry_providers: str, full_build: bool, log: str).

*   If any token in include_docs is a meta-token (the set: 'all', 'all-providers', 'apache-airflow-providers'), the wave derivation path is taken. If explicit provider tokens are mixed with meta-tokens, the meta-token wins and explicit providers are ignored.

*   In the wave path, if ref does not match the strict pattern providers/YYYY-MM-DD (exactly 4-digit year, 2-digit month, 2-digit day), return ('', True, log) where log contains the substring 'not a wave-tag pattern'.

*   In the wave path with a valid ref, call git_runner('tag', '--list', 'providers/[0-9]*-*-*', '--sort=-creatordate') to get wave-like tags. Filter the result to only those strictly matching providers/YYYY-MM-DD (the broader glob can return non-wave tags). Find the tag immediately preceding ref in the filtered list. If no predecessor exists, return ('', True, log) where log contains '::warning::No predecessor wave tag found'.

*   If a predecessor is found, call git_runner('tag', '--merged', ref, '--no-merged', prev, '--list', 'providers-*/*') to find per-provider tags between the two waves. Exclude any tag whose suffix matches rc[0-9]+$. If no final tags remain, return ('', True, log) where log contains '::warning::Wave ref {ref} has no new per-provider tags' (with the actual ref value substituted).

*   When final provider tags are found, extract provider IDs from tags matching providers-{id}/{version}. Return the IDs sorted alphabetically, space-joined, with full_build=False, and a log message containing 'Derived wave providers ({prev} -> {ref})' with the actual prev and ref values substituted.

*   When no meta-tokens are present in include_docs, use the explicit packages path: filter out tokens in the non-provider set ('apache-airflow', 'helm-chart', 'docker-stack', 'apache-airflow-ctl', 'apache-airflow-task-sdk') and in the meta-token set. Convert full package names starting with 'apache-airflow-providers-' to just the suffix as the provider ID. Convert remaining short tokens by replacing dots with hyphens. Deduplicate while preserving first-occurrence order. Return (space-joined providers, False, '').

*   For empty include_docs, return ('', False, '').

*   Provider IDs derived from the wave path must be sorted alphabetically; provider IDs from the explicit packages path must preserve input order with deduplication.


*   Interface details: Type: Function
Name: derive
Location: dev/registry/derive_wave_providers.py
Signature: derive(include_docs: str, ref: str, *, git_runner=_git) -> tuple[str, bool, str]
Description: Derives registry trigger inputs for the documentation publishing pipeline. Returns a 3-tuple of (registry_providers, full_build, log_message). registry_providers is a space-separated string of provider IDs (or "" for a full build). full_build is True when a full registry rebuild is needed. log_message is a human-readable log string (may be empty).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.