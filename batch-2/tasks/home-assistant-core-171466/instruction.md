I'm working on a security tooling script for our project that automatically checks the supply-chain integrity of Python package updates in pull requests.

*   parse_diff must accept a unified diff string and return a list of PackageChange objects; each object has a name, old_version (None if the package is newly added), and new_version attribute.

*   parse_diff must only process changes in files that match the pattern requirements*.txt (e.g., requirements_all.txt, requirements.txt, requirements_extra.txt, requirements_test.txt); changes in other files (README.md, pyproject.toml, etc.) must be ignored.

*   parse_diff must normalize package names per PEP 503: lowercase, and convert underscores/hyphens to canonical form (e.g., Foo_Bar becomes foo-bar).

*   parse_diff must ignore lines where the version does not change, lines starting with '#' (comment lines), and must strip inline comments (e.g., '# old') from requirement lines before parsing.

*   PackageChange must have a needs_agent property that returns True if and only if at least one entry in its checks dict has a status of CheckStatus.NEEDS_AGENT; all other statuses (PASS, WARN, FAIL) must return False.

*   CheckRunResult must have a needs_agent property that returns True if any of its contained packages has needs_agent == True.

*   CheckRunResult must have a to_dict() method that returns a JSON-serializable dict containing at minimum the keys 'rendered_comment', 'needs_agent', 'checks', and lowercase check kind names such as 'repo_public'.

*   fetch_package_info must fetch from https://pypi.org/pypi/{name}/{version}/json; on HTTP 404, fall back to https://pypi.org/pypi/{name}/json and set found=False with file_provenance_urls=[]; on network errors, 5xx responses, or invalid JSON on both endpoints, return found=False with project_urls={} and repo_url=None.

*   fetch_package_info must construct the integrity URL for provenance using only the first file in PyPI's urls list via the pattern https://pypi.org/integrity/{name}/{version}/{filename}/provenance, completely ignoring any urls[].provenance field in the PyPI response; if no files are listed, file_provenance_urls must be [].

*   fetch_package_info must select repo_url from project_urls by checking the 'Source' or 'Repository' keys first; if neither is present or points to a known code host, scan all values for URLs whose hostname is exactly github.com, gitlab.com, www.github.com, or www.gitlab.com; reject domain-suffix lookalikes (e.g., github.com.evil.com) and URLs where the code host appears only in a query string; return None if no valid URL is found.

*   fetch_package_info must strip backtick and newline characters from repo_url before returning.

*   check_provenance must return has_attestation=False with a detail string containing 'cannot verify' (case-insensitive) when the input package has found=False.

*   check_provenance must try each URL in file_provenance_urls sequentially; on HTTP 404 move to the next URL; if all URLs return 404, set has_attestation=False and detail='No PEP 740 provenance attestation present' (this exact substring must appear in the detail).

*   check_provenance must parse attestation_bundles[].publisher.kind from the integrity endpoint response; 'GitHub' is a recognized publisher (recognized_publisher=True); any other kind is not recognized (recognized_publisher=False); entries without a kind field must be skipped; if a valid bundle is present but no usable publisher kind can be found, set has_attestation=False and detail containing 'could not be parsed'.

*   check_provenance must strip backtick and newline characters from publisher_kind before returning.

*   render_comment must return a string that always starts with '<!-- requirements-check -->'.

*   render_comment must produce output containing 'All requirements checks passed. ✅' and a collapsed <details> (not <details open>) when every check in every package passes.

*   render_comment must produce output containing <details open> and placeholder strings of the form {{CHECK_CELL:pkg_name:check_kind_lower}} and {{CHECK_DETAIL:pkg_name:check_kind_lower}} for each NEEDS_AGENT check; no such placeholders must appear for non-NEEDS_AGENT checks.

*   render_comment must produce output containing 'No tracked requirement changes detected' when the result has no packages.

*   render_comment must show '— skipped.' for check kinds absent from a package's checks dict in the detail section and ' — |' or '| — ' in the table cell for that kind.

*   run_checks must return a CheckRunResult where: with a recognized attestation (recognized_publisher=True), CI_UPLOAD=PASS, RELEASE_PIPELINE=PASS, REPO_PUBLIC=NEEDS_AGENT, PR_LINK=NEEDS_AGENT; with no attestation, CI_UPLOAD=WARN, RELEASE_PIPELINE=NEEDS_AGENT; with an attestation from an unrecognized publisher, CI_UPLOAD=WARN, RELEASE_PIPELINE=NEEDS_AGENT and RELEASE_PIPELINE check details contain 'publisher unrecognised'.

*   run_checks must mark all four checks (CI_UPLOAD, RELEASE_PIPELINE, REPO_PUBLIC, PR_LINK) as FAIL when the package version is not found on PyPI (found=False), and result.needs_agent must be False in that case.

*   run_checks must set REPO_PUBLIC=FAIL and PR_LINK=FAIL (not NEEDS_AGENT) when the package is found on PyPI but has no repo_url; the REPO_PUBLIC check details must contain 'does not advertise'.

*   main must accept a list of string arguments including --pr-number, --diff (path to a diff file), and --output (path for JSON output); return exit code 0 on success.

*   main must write a JSON file to the --output path containing at minimum the keys 'pr_number' (integer) and 'packages' (list where each entry has a 'name' field), and print to stderr 'check_requirements: {n} package change(s)' where n is the number of detected package changes.

*   main must raise SystemExit with code 2 and print a message containing 'not found' to stderr when the --diff file does not exist.


*   Interface details: Type: Function
Name: parse_diff
Location: script/check_requirements/diff.py
Signature: parse_diff(diff_text: str) -> list[PackageChange]
Description: Parses a unified diff string and returns a list of PackageChange objects representing package version changes in tracked requirement files. Only tracks files matching `requirements*.txt` patterns. Normalizes package names per PEP 503 (e.g., `Foo_Bar` → `foo-bar`). Returns old_version=None for newly added packages. Ignores lines with no version change, comment lines (starting with `#`), and strips inline comments from requirement lines.

---

Type: Class
Name: PackageChange
Location: script/check_requirements/models.py
Description: Represents a single package version change detected in a diff.
Signature:
  Constructor: PackageChange(name: str, old_version: str | None, new_version: str, checks: dict[CheckKind, CheckResult], repo_url: str | None = None)
  Property needs_agent: bool — True if and only if at least one check has CheckStatus.NEEDS_AGENT status

---

Type: Class
Name: CheckRunResult
Location: script/check_requirements/models.py
Description: Represents the result of running all checks for a PR.
Signature:
  Constructor: CheckRunResult(pr_number: int, packages: list[PackageChange] = [])
  Property needs_agent: bool — True if any contained package has needs_agent == True
  Method to_dict() -> dict — Returns a JSON-serializable dict with at minimum the keys: "rendered_comment", "needs_agent", "checks", and lowercase check kind names (e.g., "repo_public")

---

Type: Class (Enum)
Name: CheckKind
Location: script/check_requirements/models.py
Description: Enum of check types. Values: CI_UPLOAD, RELEASE_PIPELINE, REPO_PUBLIC, PR_LINK. Serialized to lowercase strings: "ci_upload", "release_pipeline", "repo_public", "pr_link".

---

Type: Class (Enum)
Name: CheckStatus
Location: script/check_requirements/models.py
Description: Enum of check result statuses. Values: PASS, WARN, FAIL, NEEDS_AGENT.

---

Type: Class
Name: CheckResult
Location: script/check_requirements/models.py
Description: Represents the result of a single check.
Signature:
  Constructor: CheckResult(status: CheckStatus, details: str)
  Attributes: status (CheckStatus), details (str)

---

Type: Class
Name: PypiPackageInfo
Location: script/check_requirements/pypi.py
Description: Holds information fetched from PyPI for a specific package version.
Signature:
  Constructor: PypiPackageInfo(project_urls: dict, repo_url: str | None, file_provenance_urls: list[str], found: bool)
  Attributes: project_urls (dict), repo_url (str | None), file_provenance_urls (list[str]), found (bool)

---

Type: Class
Name: ProvenanceResult
Location: script/check_requirements/pypi.py
Description: Holds the result of a provenance/attestation check.
Signature:
  Constructor: ProvenanceResult(has_attestation: bool, publisher_kind: str | None, recognized_publisher: bool, detail: str)
  Attributes: has_attestation (bool), publisher_kind (str | None), recognized_publisher (bool), detail (str)

---

Type: Function
Name: fetch_package_info
Location: script/check_requirements/pypi.py
Signature: fetch_package_info(name: str, version: str) -> PypiPackageInfo
Description: Fetches package metadata from PyPI.
- Versioned endpoint: https://pypi.org/pypi/{name}/{version}/json
- Latest endpoint (fallback): https://pypi.org/pypi/{name}/json
- Integrity URL pattern: https://pypi.org/integrity/{name}/{version}/{filename}/provenance
- On versioned 404: falls back to latest endpoint, sets found=False, file_provenance_urls=[]
- On both 404, 5xx, network error, or invalid JSON: returns found=False, project_urls={}, repo_url=None
- Selects repo_url from project_urls by key ("Source" or "Repository" preferred), then by value scanning for known code hosting domains (github.com, gitlab.com, including www. subdomains)
- Rejects domain-suffix lookalikes (e.g., github.com.evil.com) and query-string matches (e.g., evil.com/?x=github.com)
- Strips backtick and newline characters from repo_url
- Builds file_provenance_urls from the FIRST file in PyPI's urls list using the filename; ignores the urls[].provenance field entirely
- Returns empty file_provenance_urls if PyPI lists no files

---

Type: Function
Name: check_provenance
Location: script/check_requirements/pypi.py
Signature: check_provenance(pkg: PypiPackageInfo) -> ProvenanceResult
Description: Checks provenance attestations for a package.
- If pkg.found is False: returns has_attestation=False with detail containing "cannot verify" (case-insensitive)
- Tries each URL in pkg.file_provenance_urls sequentially
- On 404 response: moves to next URL
- If all URLs return 404: has_attestation=False, detail="No PEP 740 provenance attestation present" (this exact string)
- Parses attestation_bundles[].publisher.kind from JSON response
- "GitHub" kind → recognized_publisher=True
- Unknown kind → recognized_publisher=False, publisher_kind set to the unknown kind value
- Entries in attestation_bundles without a publisher.kind field are skipped
- Bundle present but no usable publisher kind: has_attestation=False, detail contains "could not be parsed"
- Strips backtick and newline characters from publisher_kind

---

Type: Function
Name: render_comment
Location: script/check_requirements/render.py
Signature: render_comment(result: CheckRunResult) -> str
Description: Renders a formatted comment string from a CheckRunResult.
- Always starts with "<!-- requirements-check -->"
- When all checks pass: contains "All requirements checks passed. ✅", uses collapsed <details> (not <details open>)
- When any check is NEEDS_AGENT: uses <details open>, emits placeholder strings {{CHECK_CELL:pkg_name:check_kind_lower}} and {{CHECK_DETAIL:pkg_name:check_kind_lower}} for each NEEDS_AGENT check
- No unresolved {{CHECK_CELL or {{CHECK_DETAIL placeholders appear for non-NEEDS_AGENT checks
- When result has no packages: contains "No tracked requirement changes detected"
- For check kinds absent from pkg.checks: shows "— skipped." in detail bullets and " — |" or "| — " in table cells

---

Type: Function
Name: run_checks
Location: script/check_requirements/runner.py
Signature: run_checks(pr_number: int, diff_text: str) -> CheckRunResult
Description: Parses a diff, fetches PyPI info, and runs all checks for each changed package.
- With recognized attestation (recognized_publisher=True): CI_UPLOAD=PASS, RELEASE_PIPELINE=PASS, REPO_PUBLIC=NEEDS_AGENT, PR_LINK=NEEDS_AGENT
- With no attestation (has_attestation=False): CI_UPLOAD=WARN, RELEASE_PIPELINE=NEEDS_AGENT
- With attestation but unrecognized publisher: CI_UPLOAD=WARN, RELEASE_PIPELINE=NEEDS_AGENT, RELEASE_PIPELINE check details contain "publisher unrecognised"
- When found=False (version not on PyPI): CI_UPLOAD=FAIL, RELEASE_PIPELINE=FAIL, REPO_PUBLIC=FAIL, PR_LINK=FAIL; needs_agent=False
- When package is found but repo_url is None: REPO_PUBLIC=FAIL, PR_LINK=FAIL; REPO_PUBLIC check details contain "does not advertise"

---

Type: Function
Name: main
Location: script/check_requirements/__main__.py
Signature: main(args: list[str]) -> int
Description: CLI entry point for the check_requirements tool.
- Accepts arguments: --pr-number INT, --diff PATH, --output PATH
- Returns 0 on success
- Writes a JSON file to the --output path containing at minimum: pr_number (int) and packages list (each with a "name" field)
- Prints to stderr: "check_requirements: {n} package change(s)" where n is the number of changed packages
- Raises SystemExit with exit code 2 if the --diff file does not exist; stderr output contains "not found"


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.