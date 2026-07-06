I'm working on the CI script that automatically upgrades important package versions in our project.

*   The _parse_duration_hours function must return the duration as a float number of hours: 'N hours' returns N as float, '1 hour' returns 1.0, 'N days' returns N * 24.0, '30 minutes' returns 0.5.

*   The _parse_duration_hours function must return None for inputs that are not valid duration strings, including ISO 8601 datetime strings (e.g. '2026-05-21T21:58:56Z'), arbitrary text ('garbage'), and empty string ('').

*   The _parse_manual_overrides function must parse a pyproject.toml content string and return a dict mapping each package name to its cooldown in hours (float). Only entries whose TOML value is a duration string are included; entries with a boolean value such as false must be excluded. For example, if the content contains 'uv = "12 hours"' and 'starlette = "6 hours"' as manual overrides and 'apache-airflow = false' as an auto-generated entry, the result must be {"uv": 12.0, "starlette": 6.0}.

*   The _remove_override_entry function must remove the given package's override entry line (e.g. 'starlette = "6 hours"') from the content, along with any '# REMOVE BY ...' comment lines immediately preceding it, in every section where it appears.

*   The _remove_override_entry function must remove the entry from both the [tool.uv.exclude-newer-package] and [tool.uv.pip.exclude-newer-package] sections. All other content must remain unchanged: other package entries, their associated comments, boolean (false) entries, and all TOML section headers.

*   The _remove_override_entry function must be idempotent: calling it twice with the same package name must produce exactly the same string as calling it once.

*   The _remove_override_entry function must be a no-op when the package name is not found in the content: it must return the original string unchanged.

*   The _is_version_within_cooldown function must accept an optional cooldown_hours parameter (default 96, representing a 4-day global window). When no cooldown_hours is supplied, a version published 2 days ago (48 hours) must be considered within the cooldown and the function must return True. When cooldown_hours=6 is supplied, the same version (published 48 hours ago) is outside the 6-hour window and the function must return False.


*   Interface details: Type: Function
Name: _parse_duration_hours
Location: scripts/ci/prek/upgrade_important_versions.py
Signature: _parse_duration_hours(value: str) -> float | None
Description: Parses a human-readable duration string (e.g., "12 hours", "1 day", "30 minutes") and returns the equivalent number of hours as a float. Returns None for inputs that do not match a recognised duration format (e.g., ISO datetime strings, arbitrary text, empty string). Supported units: minutes/minute, hours/hour, days/day. Conversion: 1 day = 24 hours, 1 minute = 1/60 hour.

Type: Function
Name: _parse_manual_overrides
Location: scripts/ci/prek/upgrade_important_versions.py
Signature: _parse_manual_overrides(content: str) -> dict[str, float]
Description: Scans a pyproject.toml content string for manually added per-package cooldown overrides in the exclude-newer-package sections and returns a dict mapping package names to their cooldown duration in hours (float). Only entries whose value is a duration string are included; entries whose value is a boolean (e.g., false) are skipped. If the same package appears in multiple sections the last encountered value wins (the result is a plain dict, so duplicate keys just overwrite).

Type: Function
Name: _remove_override_entry
Location: scripts/ci/prek/upgrade_important_versions.py
Signature: _remove_override_entry(content: str, package: str) -> str
Description: Returns a new version of the pyproject.toml content with the specified package's override entry removed from every occurrence. For each occurrence the function also removes any contiguous "# REMOVE BY …" comment lines that appear immediately before the entry line. The removal is applied to both [tool.uv.exclude-newer-package] and [tool.uv.pip.exclude-newer-package] sections. Other entries, boolean (false) entries, and all section headers are left untouched. The function is idempotent: calling it twice with the same package name produces the same output as calling it once. If the package is not present, the original content is returned unchanged.

Type: Function
Name: _is_version_within_cooldown
Location: scripts/ci/prek/upgrade_important_versions.py
Signature: _is_version_within_cooldown(releases: dict, version: str, cooldown_hours: int = 96) -> bool
Description: Determines whether a package version is still within the cooldown window (i.e., was released too recently to upgrade to). The `releases` argument follows the PyPI JSON API shape: a dict mapping version strings to a list of release-file dicts, each containing an "upload_time_iso_8601" key with a UTC timestamp string ending in "Z". Returns True if the version's upload time is within the cooldown window from now, False otherwise. The default cooldown is 96 hours (4 days). Pass a different value for `cooldown_hours` to apply a per-package override.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.