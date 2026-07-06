I'm building an integration between Soda data quality checks and Dagster, and I need a new library that provides a Dagster component for running Soda scans.

*   Must create a new Python package at python_modules/libraries/dagster-soda/ containing a dagster_soda package that exports SodaScanComponent from its top-level __init__.py.

*   The _sanitize_check_name function must replace all characters that are not alphanumeric or underscore with '_', truncate the result to 100 characters, strip leading and trailing underscores, and return the string 'check' if the result would otherwise be empty.

*   The _parse_check_identifiers_from_yaml function must parse a SodaCL YAML file for a 'checks for {dataset}' key; for plain string list items it must sanitize each string via _sanitize_check_name; for dict items where the config contains a 'name' key, it must use that name value directly; it must return an empty list when the dataset key is absent or the file is empty.

*   The _collect_check_identifiers_for_dataset function must aggregate sanitized check identifiers from all provided YAML file paths for the given dataset name, resolving relative paths against project_root.

*   The _get_outcome function must return the string value of soda_check.outcome if that attribute exists, otherwise fall back to soda_check.result, and return 'unknown' if neither attribute is present.

*   The _get_check_name function must return the string value of soda_check.name if present, otherwise fall back to soda_check.check_name.

*   The _get_severity function must return the string value of soda_check.severity if present, otherwise return the default_severity parameter (which defaults to 'warn').

*   The _to_dagster_severity function must map the strings 'error' and 'fail' to AssetCheckSeverity.ERROR, and map 'warn', 'pass', or any other value to AssetCheckSeverity.WARN.

*   The _filter_results_for_dataset function must filter the scan_results.checks list to only include results whose 'table' attribute (or 'table_name' if 'table' is absent) matches the given dataset string.

*   SodaScanComponent.build_defs must return a Definitions object containing one multi-asset check definition per configured dataset. Each check definition must include one AssetCheckSpec per check found in the YAML files, with the check name set to the sanitized identifier and the asset key resolved from asset_key_map using AssetKey.from_user_string() (e.g., 'dagster/asset/key' resolves to AssetKey(['dagster', 'asset', 'key'])).

*   During execution, the multi-asset check must create a Scan instance (from soda.scan) and call scan.execute(). After execution, it must retrieve results via scan.get_scan_results() if that method exists, otherwise via scan.build_scan_results(). A 'pass' outcome must produce passed=True; 'fail' and 'warn' outcomes must produce passed=False.

*   When a Soda check result exists for a spec, the AssetCheckResult must include a 'severity' metadata entry whose text value is the severity string (e.g., 'error', 'warn') derived from the check result or the component's default_severity when severity is absent from the result.

*   When no Soda check result matches a spec name, the AssetCheckResult must be passed=False and include a metadata key 'error' with the exact text value "No matching Soda result for check '{check_name}'" (where {check_name} is the spec name).

*   When scan.execute() raises an exception, all spec names for the dataset must yield passed=False AssetCheckResult objects, each with a metadata key 'error' whose text value contains 'Soda scan failed' and also contains the original exception message.

*   SodaScanComponent must support being loaded from a defs.yaml file with type 'dagster_soda.SodaScanComponent' and attributes checks_paths, configuration_path, data_source_name, and asset_key_map. The loaded component must expose these values as instance attributes.

*   When scaffolded, SodaScanComponent must write a checks.yml file at the target defs path containing at least one 'checks for my_table' entry, and the generated defs.yaml must reference asset_key_map with 'my_table' mapped to 'my_table'.


*   Interface details: ## Module Location

All code must be placed in `python_modules/libraries/dagster-soda/dagster_soda/component.py`.

`SodaScanComponent` must also be re-exported from `python_modules/libraries/dagster-soda/dagster_soda/__init__.py` so that `from dagster_soda import SodaScanComponent` works.

---

## Functions required in `dagster_soda.component`

Type: Function
Name: _sanitize_check_name
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _sanitize_check_name(name: str) -> str
Description: Converts any string to a valid Dagster check name. Replaces all characters that are not alphanumeric or underscore with `_`, truncates to 100 characters, strips leading/trailing underscores, and returns "check" if the result is empty.

Type: Function
Name: _parse_check_identifiers_from_yaml
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _parse_check_identifiers_from_yaml(yaml_path: Path, dataset: str) -> list[str]
Description: Reads a SodaCL YAML file and returns a list of sanitized check identifiers for the given dataset. Looks for the key "checks for {dataset}" in the YAML. For plain string items, sanitizes via _sanitize_check_name. For dict items with a "name" key in the config, uses that name value. Returns [] if the dataset key is not found or the file is empty.

Type: Function
Name: _collect_check_identifiers_for_dataset
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _collect_check_identifiers_for_dataset(checks_paths: list[str], project_root: Path, dataset: str) -> list[str]
Description: Aggregates check identifiers from multiple SodaCL YAML files for the given dataset. Resolves relative paths against project_root.

Type: Function
Name: _get_outcome
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _get_outcome(soda_check: Any) -> str
Description: Extracts outcome from a Soda check result object. Tries the `outcome` attribute first, then `result`, then returns "unknown".

Type: Function
Name: _get_check_name
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _get_check_name(soda_check: Any) -> str
Description: Extracts check name from a Soda check result object. Tries `name` attribute first, then `check_name`.

Type: Function
Name: _get_severity
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _get_severity(soda_check: Any, default_severity: str = "warn") -> str
Description: Extracts severity from a Soda check result object. Returns `soda_check.severity` if present, otherwise returns the `default_severity` parameter (defaults to "warn").

Type: Function
Name: _to_dagster_severity
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _to_dagster_severity(severity: str) -> AssetCheckSeverity
Description: Converts a Soda severity string to a Dagster AssetCheckSeverity. "error" or "fail" maps to AssetCheckSeverity.ERROR; "warn" or "pass" (and any other value) maps to AssetCheckSeverity.WARN.

Type: Function
Name: _filter_results_for_dataset
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Signature: _filter_results_for_dataset(scan_results: Any, dataset: str) -> list[Any]
Description: Filters Soda scan results to only those matching the given dataset. For each check result, checks the `table` attribute first, then `table_name`. Returns a list of matching check results.

---

## Class required in `dagster_soda.component`

Type: Class
Name: SodaScanComponent
Location: python_modules/libraries/dagster-soda/dagster_soda/component.py
Description: A Dagster component that runs Soda Core scans and maps SodaCL check results to Dagster asset checks. Must be a Dagster Component (inheriting from appropriate base classes) supporting defs.yaml loading as type "dagster_soda.SodaScanComponent".

Constructor signature: SodaScanComponent(checks_paths: list[str], configuration_path: str, data_source_name: str, asset_key_map: dict[str, str] | None = None, default_severity: str = "warn")

Accessible attributes: checks_paths, configuration_path, data_source_name, asset_key_map, default_severity

Method: build_defs(context: ComponentLoadContext) -> Definitions
Description: Builds a Dagster Definitions object containing multi-asset checks for each configured dataset. Parses SodaCL YAML files, creates AssetCheckSpec objects, and registers a callable that runs the Soda scan at execution time.

Scaffolding: When scaffolded, must write a `checks.yml` file at `defs_path / "checks.yml"` containing "checks for my_table" with at least one check. The generated defs.yaml must reference `asset_key_map: {"my_table": "my_table"}` and the checks path relative to the project root.

Asset key resolution: The `asset_key_map` values are resolved using `AssetKey.from_user_string()`, so "/" in the value creates multi-part asset keys (e.g., "dagster/asset/key" → AssetKey(["dagster", "asset", "key"])).

The `Scan` class (from soda.scan) must be imported in `dagster_soda/component.py` at module level so that `dagster_soda.component.Scan` is patchable.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.