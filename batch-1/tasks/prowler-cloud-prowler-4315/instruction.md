Refactor the outputs system in a multi-cloud security scanner by implementing a unified finding model and a consistent interface for output formats. Replace the legacy finding model with a new Pydantic BaseModel that handles provider-specific data mapping and output generation. Create a proper abstract base class for output formats and a CSV output class that correctly formats and writes findings data.

*   Implement a new `Finding` class at `prowler/lib/outputs/finding.py`:
    *   Use Pydantic BaseModel with fields: auth_method, timestamp, account_uid, account_name, account_email, account_organization_uid, account_organization_name, account_tags, finding_uid, provider, check_id, check_title, check_type, status, status_extended, muted, service_name, subservice_name, severity, resource_type, resource_uid, resource_name, resource_details, resource_tags, partition, region, description, risk, related_url, remediation_recommendation_text, remediation_recommendation_url, remediation_code_nativeiac, remediation_code_terraform, remediation_code_cli, remediation_code_other, compliance, categories, depends_on, related_to, notes, prowler_version.
    *   Define `Status` and `Severity` as StrEnum with values PASS, FAIL, MANUAL and critical, high, medium, low, informational, respectively.
    *   Implement `generate_output(cls, provider, check_output)` to construct a `Finding` from provider-specific data:
        *   AWS: auth_method = "profile: {value}", resource_name = check_output.resource_id, resource_uid = check_output.resource_arn, region = check_output.region.
        *   Azure: auth_method = "{identity_type}: {identity_id}", resource_name = check_output.resource_name, resource_uid = check_output.resource_id, region = check_output.location.
        *   GCP: auth_method = "Principal: {value}", account_uid = project.id, account_name = project.name, account_tags = project.labels, resource_name = check_output.resource_name, resource_uid = check_output.resource_id, region = check_output.location.
        *   Kubernetes: auth_method = "in-cluster" when context == "In-Cluster" else "kubeconfig", region = "namespace: {check_output.namespace}", account_name = "context: {provider.identity.context}", resource_name = check_output.resource_name, resource_uid = check_output.resource_id.

*   Update the `generate_csv_fields` function in `prowler/lib/outputs/csv/csv.py` to accept the `Finding` class and return its field names in definition order.

*   Create a new `Output` abstract base class at `prowler/lib/outputs/output.py`:
    *   Constructor must accept (findings, create_file_descriptor=False, file_path=None), call `self.transform(findings)` automatically, and open `file_path` as `TextIOWrapper` if `create_file_descriptor=True`.
    *   Expose `data` and `file_descriptor` as properties.
    *   Define abstract methods `transform(self, findings)` and `batch_write_data_to_file(self, file_descriptor)`.

*   Extend `Output` with a `CSV` class at `prowler/lib/outputs/csv/models.py`:
    *   Implement `transform(self, findings)` to convert each `Finding` to a dict with uppercase field keys, joining `account_tags` with " | ", formatting `compliance` as "key: value" entries, and converting `status` and `severity` to strings.
    *   Implement `batch_write_data_to_file(self)` to write header and rows to `self._file_descriptor` using ";" as the delimiter.

*   Modify the `write_csv` function in `prowler/lib/outputs/csv/csv.py` to accept either a dict or an object (with `__dict__`) as the row argument and write semicolon-separated values to the given file descriptor.

*   Ensure `fill_file_descriptors` no longer creates a file descriptor for CSV output mode.

*   Update `tests/lib/outputs/fixtures/fixtures.py` to import and use `Finding` and ensure `generate_finding_output` returns a `Finding`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.