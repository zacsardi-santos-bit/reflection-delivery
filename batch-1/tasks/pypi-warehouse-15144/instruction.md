Implement support for Google Cloud as a trusted publisher in PyPI, allowing project maintainers to register Google Cloud workload identities. Update management views to handle Google-based publishers similarly to GitHub Actions, and ensure administrators can independently enable or disable Google trusted publishing.

*   Add a new admin flag constant `AdminFlagValue.DISALLOW_GOOGLE_OIDC` to independently disable Google trusted publishing.
*   Update account and project publishing management views:
    *   Include a 'disabled' dictionary with 'GitHub' and 'Google' boolean keys in the response, using `disallow_oidc()` checks.
    *   Incorporate 'pending_google_publisher_form' and 'google_publisher_form' in the respective views.
*   Ensure `manage_publishing()` and `manage_project_oidc_publishers()` methods call `disallow_oidc()` without arguments for overall checks before per-provider checks.
*   Implement `ManageAccountPublishingViews.add_pending_google_oidc_publisher()`:
    *   Access `default_response` to trigger OIDC checks.
    *   Check `disallow_oidc(AdminFlagValue.DISALLOW_GOOGLE_OIDC)` and flash an error message if disabled.
    *   Create `PendingGooglePublisher` with specified fields on success.
*   Implement `ManageOIDCPublisherViews.add_google_oidc_publisher()`:
    *   Check `disallow_oidc(AdminFlagValue.DISALLOW_GOOGLE_OIDC)` and flash an error message if disabled.
    *   Create or find a `GooglePublisher` and flash a success message.
*   Ensure `ManageOIDCPublisherViews` has a `google_publisher_form` attribute initialized with `GooglePublisherForm`.
*   Define `PendingGooglePublisher` and `GooglePublisher` as SQLAlchemy models with specified attributes and methods.
*   Ensure `PendingGooglePublisherForm` and `GooglePublisherForm` are accessible and have specified attributes and parameters.
*   Use the exact string 'publisher:Google' for all metric increment tags related to Google publisher operations.
*   Ensure error flash messages follow the format: '{publisher_name}-based trusted publishing is temporarily disabled. See https://pypi.org/help#admin-intervention for details.'
*   Support deletion operations for both `PendingGitHubPublisher` and `PendingGooglePublisher` types, querying the database using `OIDCPublisher`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.