Implement a utility class to automate the publishing of Airflow documentation packages to cloud object storage. Ensure the utility can list available packages, exclude specified ones, and handle versioned and bulk publishing modes with overwrite control.

*   Implement the `S3DocsPublish` class in `dev/breeze/src/airflow_breeze/utils/publish_docs_to_s3.py`.
    *   Constructor parameters: `source_dir_path` (str), `destination_location` (str), `exclude_docs` (str), `dry_run` (bool), `overwrite` (bool), `parallelism` (int).
    *   Store all parameters as instance attributes.

*   Implement the `get_all_docs` property.
    *   Return a list of directory/file names found in `source_dir_path` using `os.listdir`.
    *   Raise `SystemExit` if `source_dir_path` does not exist.

*   Implement the `get_all_excluded_docs` property.
    *   Split `exclude_docs` by commas and return the list of strings.

*   Implement the `get_all_eligible_docs` property.
    *   Return docs from `get_all_docs` that do not match any pattern from `get_all_excluded_docs`.
    *   Use substring matching; treat dots in patterns as hyphens.
    *   Raise `SystemExit` if no eligible docs remain after filtering.

*   Implement the `doc_exists` method.
    *   Return a boolean indicating if a doc already exists at the destination.

*   Implement the `publish_stable_version_docs` method.
    *   Iterate over eligible docs, read each doc's version from a file.
    *   If `doc_exists` returns False or `overwrite` is True, append two tuples to `source_dest_mapping`: 
        *   `('{source_dir_path}/{doc}/{version}/', '{destination_location}/{doc}/{version}/')`
        *   `('{source_dir_path}/{doc}/{version}/', '{destination_location}/{doc}/stable/')`
    *   Call `run_publish` for each entry.
    *   Ensure `source_dest_mapping` is empty if no eligible docs exist.

*   Implement the `publish_all_docs` method.
    *   Iterate over eligible docs.
    *   If `doc_exists` returns False or `overwrite` is True, append one tuple to `source_dest_mapping`: 
        *   `('{source_dir_path}/{doc}/', '{destination_location}/{doc}/')`
    *   Call `run_publish` for each entry.
    *   Ensure `source_dest_mapping` is empty if no eligible docs exist.

*   Maintain the `source_dest_mapping` attribute.
    *   It should be a list of (source_path, destination_path) tuples reflecting processed path pairs during publishing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.