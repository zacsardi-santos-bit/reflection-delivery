I need to create a small internal Python library at dev/pypi/ that development scripts in the repository can use to fetch package metadata from the Python package index's JSON API.

*   The `get_package(name)` function must fetch package metadata from the PyPI JSON API and return a `Package` object. The returned `Package` must have a `latest_version` attribute that is the highest stable (non-pre-release, non-dev, non-yanked) version as a `packaging.version.Version` object, or `None` if no stable version exists.

*   The `get_package` function must cache results in memory. Calling `get_package` twice with the same package name must make only one HTTP request.

*   The `clear_cache()` function must invalidate the in-memory cache. After calling `clear_cache()`, the next call to `get_package` for any package name must make a fresh HTTP request.

*   The HTTP endpoint URL must be constructed as `{base_url}/pypi/{name}/json`. The base URL defaults to `https://pypi.org`. When the environment variable `PYPI_URL` is set, it overrides the base URL (any trailing slash must be stripped before building the URL).

*   A 404 HTTP response must raise `PyPIError` immediately (without any retry) with a message that contains the substring `"Package not found"`.

*   A non-retryable HTTP error (e.g., status 401) must raise `PyPIError` immediately (without any retry) with a message that contains `"HTTP {status_code}"` (e.g., `"HTTP 401"`).

*   Transient HTTP errors with status codes 429, 500, 502, 503, and 504 must be retried. Network errors such as `URLError` and `ConnectionResetError` must also be retried. The maximum total number of attempts is 3 (initial + retries). Between retries, `time.sleep` must be called (via a module-level `import time` in `pypi._client`, not via `from time import sleep`).

*   After all 3 attempts are exhausted without success, `PyPIError` must be raised with a message containing the substring `"after 3 attempts"`.

*   If the HTTP response body parses as valid JSON but is not a dict (e.g., a list), `PyPIError` must be raised with a message containing `"Unexpected response"`.

*   The `aget_package(name)` async function must return a `Package` object equivalent to what `get_package(name)` returns.

*   The `aget_packages(names)` async function must fetch each package in `names`, and return a list of `Package` objects in the same order as `names`.

*   `Package.from_json(data)` must parse the PyPI JSON response dict. Releases with no distributions (empty list) must be dropped. Releases where no distribution has a parseable `upload_time_iso_8601` field must be dropped. Releases with version strings that cannot be parsed as valid PEP 440 versions must be dropped.

*   `Package.versions` must return a tuple of `packaging.version.Version` objects for all valid releases, sorted in ascending version order.

*   `Package.latest_version` must return the highest version that is not a pre-release, not a dev release, and not yanked. It must return `None` if no such version exists.

*   `Package.get_release(version)` must accept either a `str` or a `packaging.version.Version` and return the matching `Release` object, or `None` if no release with that version exists.

*   A `Release` object must have: `upload_time` (a datetime representing the earliest upload time across all distributions for that version); `yanked` (a bool that is `True` only if every distribution for the release is yanked, `False` if any distribution is not yanked); `requires_python` (a `packaging.specifiers.SpecifierSet` parsed from the first non-empty, valid `requires_python` field across distributions, or `None` if all are absent, empty, or invalid).


*   Interface details: ## Public Module: `pypi`

Type: Module
Name: pypi
Location: dev/pypi/src/pypi/__init__.py
Description: Public API surface. Must re-export `get_package`, `clear_cache`, `aget_package`, `aget_packages`, `Package`, and `PyPIError` so they are importable directly as `pypi.<name>`.

---

## Client Functions

Type: Function
Name: get_package
Location: dev/pypi/src/pypi/_client.py
Signature: get_package(name: str) -> Package
Description: Fetches package metadata from the PyPI JSON API for the given package name. Results are memoized in an in-memory cache so that repeated calls with the same name make only one HTTP request. Raises `PyPIError` on unrecoverable errors.

Type: Function
Name: clear_cache
Location: dev/pypi/src/pypi/_client.py
Signature: clear_cache() -> None
Description: Clears the in-memory cache of `get_package`. After calling this, the next `get_package` call for any name will make a fresh HTTP request.

Type: Function
Name: aget_package
Location: dev/pypi/src/pypi/_client.py
Signature: aget_package(name: str) -> Package  [async]
Description: Async wrapper around `get_package`. Returns a `Package` object.

Type: Function
Name: aget_packages
Location: dev/pypi/src/pypi/_client.py
Signature: aget_packages(names: Iterable[str]) -> list[Package]  [async]
Description: Fetches multiple packages concurrently. Returns results as a list whose order matches the input `names` iterable.

Type: Class
Name: PyPIError
Location: dev/pypi/src/pypi/_client.py
Description: Exception class (subclass of RuntimeError) raised for all unrecoverable errors when fetching from PyPI.

---

## Data Models

Type: Class
Name: Package
Location: dev/pypi/src/pypi/_models.py
Description: Typed representation of a PyPI package and its releases.
Signatures:
  - Package.from_json(data: dict[str, Any]) -> Package  [classmethod]
  - Package.versions -> tuple[Version, ...]  [property]
  - Package.latest_version -> Version | None  [property]
  - Package.get_release(version: str | Version) -> Release | None

Type: Class
Name: Release
Location: dev/pypi/src/pypi/_models.py
Description: Typed representation of a single package release (one version). Attributes:
  - upload_time: datetime
  - yanked: bool
  - requires_python: SpecifierSet | None

---

## Implementation Constraints

### Sleep Patching Requirement
The file `dev/pypi/src/pypi/_client.py` MUST import the `time` standard library as a module-level name using `import time` (not `from time import sleep`). Sleep calls must be `time.sleep(...)`. This is required because the test infrastructure patches `pypi._client.time.sleep` to disable actual sleeping during tests. Using `from time import sleep` will cause the entire test suite to fail at setup.

### URL Construction
The HTTP endpoint URL is constructed as `{base_url}/pypi/{name}/json`. The base URL defaults to `https://pypi.org` and can be overridden via the `PYPI_URL` environment variable. Any trailing slash in `PYPI_URL` must be stripped before constructing the URL so that the final URL is `{stripped_base}/pypi/{name}/json`.

### Exact Error Message Patterns
- A 404 response must raise `PyPIError` with a message containing the substring `"Package not found"`.
- A non-retryable HTTP error (e.g., 401) must raise `PyPIError` with a message containing `"HTTP {status_code}"` (e.g., `"HTTP 401"`).
- After all retry attempts are exhausted, the error message must contain `"after 3 attempts"`.
- When the JSON response is not a dict (e.g., a list), the error message must contain `"Unexpected response"`.

### Retry Count
The maximum number of attempts (initial + retries) is exactly **3**. Transient status codes 429, 500, 502, 503, 504 and network errors (`URLError`, `ConnectionResetError`) are retried. Status 404 is NOT retried (raises immediately). Other non-retryable HTTP errors (e.g., 401) are also NOT retried.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.