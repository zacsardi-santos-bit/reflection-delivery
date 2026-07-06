I'm working on an organization review system and I need to add redirect detection for checkout URLs.

*   A new schema type `UrlRedirectInfo` must be defined in `polar/organization_review/schemas.py` with the following fields: `original_url` (str, required), `final_url` (str or None, default None), `final_domain` (str or None, default None), `redirected` (bool, default False), and `error` (str or None, default None).

*   The `CheckoutSuccessUrlData` schema must include a `redirect_results` field holding a list of `UrlRedirectInfo` objects, defaulting to an empty list.

*   The `CheckoutReturnUrlData` schema must include a `redirect_results` field holding a list of `UrlRedirectInfo` objects, defaulting to an empty list.

*   A new async function `resolve_url_redirects` must be defined in `polar/organization_review/collectors/setup.py`. It accepts a list of URL strings and returns a list of `UrlRedirectInfo` objects. When given an empty list it must immediately return an empty list.

*   When `resolve_url_redirects` is called with a URL that responds without redirecting, the result for that URL must have `redirected=False` and `final_domain` set to the domain portion of the original URL.

*   When `resolve_url_redirects` is called with a URL that redirects to a different domain, the result must have `redirected=True`, `final_domain` set to the destination domain, and `final_url` set to the final URL after all redirects.

*   When `resolve_url_redirects` is called with a URL that redirects to a different path on the same domain, the result must have `redirected=False`.

*   When `resolve_url_redirects` encounters any exception during resolution (such as a timeout or connection error), it must return a result with `error` set to a non-None string and `redirected=False`. The function must not propagate the exception.

*   The `collect_setup_data` function must accept two new keyword-only arguments: `success_url_redirects` (list of `UrlRedirectInfo` or None, default None) and `return_url_redirects` (list of `UrlRedirectInfo` or None, default None). When provided, these lists are stored verbatim as `redirect_results` on `checkout_success_urls` and `checkout_return_urls` respectively. When not provided, `redirect_results` defaults to an empty list.

*   The module `polar/organization_review/collectors/setup.py` must expose an async function named `_validate_url_host` that validates a URL's host. This function must be independently patchable (e.g., for bypassing DNS resolution in test environments).


*   Interface details: Type: Class
Name: UrlRedirectInfo
Location: server/polar/organization_review/schemas.py
Description: Schema that tracks where a URL ultimately redirects to. All fields except original_url have defaults.
Fields:
  - original_url: str  (required)
  - final_url: str | None = None
  - final_domain: str | None = None
  - redirected: bool = False
  - error: str | None = None

Type: Class (modified)
Name: CheckoutSuccessUrlData
Location: server/polar/organization_review/schemas.py
Description: Existing schema extended with redirect tracking. New field added:
  - redirect_results: list[UrlRedirectInfo] = Field(default_factory=list)

Type: Class (modified)
Name: CheckoutReturnUrlData
Location: server/polar/organization_review/schemas.py
Description: Existing schema extended with redirect tracking. New field added:
  - redirect_results: list[UrlRedirectInfo] = Field(default_factory=list)

Type: Function
Name: resolve_url_redirects
Location: server/polar/organization_review/collectors/setup.py
Signature: async def resolve_url_redirects(urls: list[str]) -> list[UrlRedirectInfo]
Description: Follows HTTP redirects for a list of URLs and returns where each ultimately lands. Returns an empty list immediately for empty input. For each URL, returns a UrlRedirectInfo with redirected=True if the final domain differs from the original, redirected=False if the domain is unchanged or the URL does not redirect, and error set to a non-None string if any exception occurs during resolution (the function never propagates exceptions).

Type: Function (modified)
Name: collect_setup_data
Location: server/polar/organization_review/collectors/setup.py
Signature: collect_setup_data(checkout_links: list[CheckoutLink], checkout_return_urls: list[str], checkout_success_urls: list[str], api_key_count: int, webhook_endpoints: list[WebhookEndpoint], *, success_url_redirects: list[UrlRedirectInfo] | None = None, return_url_redirects: list[UrlRedirectInfo] | None = None) -> SetupData
Description: Existing function extended with two new keyword-only arguments. When success_url_redirects is provided, its contents are stored in result.checkout_success_urls.redirect_results. When return_url_redirects is provided, its contents are stored in result.checkout_return_urls.redirect_results. Both default to an empty list when not supplied.

Type: Function (internal — must be patchable by name)
Name: _validate_url_host
Location: server/polar/organization_review/collectors/setup.py
Signature: async def _validate_url_host(url: str) -> None
Description: Internal async function that validates a URL's host does not resolve to a private or reserved IP address (SSRF protection). Must be defined as a module-level function so tests can patch it by name to bypass DNS resolution in CI environments.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.