Implement enhancements to the changelog generation and hosting service integrations by addressing gaps in issue URL generation, method naming, and Bitbucket support. Ensure the base hosting interface is abstract and cannot be instantiated directly.

*   Implement issue URL generation:
    *   Add `issue_url(self, issue_num: str | int) -> str` method in `Github` class, returning URLs in the format `{server}/{owner}/{repo}/issues/{issue_num}`.
    *   Add `issue_url(self, issue_num: str | int) -> str` method in `Gitea` class, returning URLs in the format `{server}/{owner}/{repo}/issues/{issue_num}`.
    *   Ensure both methods accept integer and string issue numbers.

*   Rename method for clarity:
    *   Rename `upload_asset` to `upload_release_asset` in both `Github` and `Gitea` classes.
    *   Update `upload_dists` method to call `upload_release_asset` instead of `upload_asset`.

*   Enhance Bitbucket support:
    *   Add `DEFAULT_API_URL_CLOUD` constant with value `'https://api.bitbucket.org/2.0'` in `Bitbucket` class.
    *   Ensure `Bitbucket` class handles custom domain configurations, including:
        *   Explicit API path (e.g., `example.com/rest/api/1.0`).
        *   API as a subdomain (e.g., `api.example.com`).
        *   Automatic API URL derivation (appending `/rest/api/1.0`).
        *   Path-prefix-based server addresses (e.g., `special.custom.server/bitbucket`).
        *   Insecure HTTP connections, both explicitly specified and inferred.

*   Abstract base class enforcement:
    *   Make `HvcsBase` class abstract using `ABCMeta` or equivalent.
    *   Declare `remote_url(self, use_token: bool) -> str` and `get_changelog_context_filters(self) -> tuple[Callable[..., Any], ...]` as abstract methods.

*   Changelog context and filters:
    *   Implement `make_changelog_context(hvcs_client: HvcsBase, release_history: ReleaseHistory) -> ChangelogContext` in `semantic_release/changelog/context.py`.
    *   Ensure `ChangelogContext` includes `hvcs_type: str` attribute and `bind_to_environment(env)` method.
    *   Register HVCS-specific Jinja2 filters in the environment after calling `bind_to_environment`.
    *   Implement `get_changelog_context_filters(self) -> tuple[Callable[..., Any], ...]` in each HVCS class to return callable methods for Jinja2 filters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.