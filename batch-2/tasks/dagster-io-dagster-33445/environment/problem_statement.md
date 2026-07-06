## Description

We need a new integration library that bridges Soda data quality checks with Dagster's asset check system. Right now, teams using Soda for data quality have no native way to run their existing Soda check files within Dagster and see the individual check results surfaced as Dagster asset checks. They have to write custom glue code every time.

## Expected Behavior

- A new Dagster component that can be configured with one or more Soda check YAML files, a data source configuration file, a data source name, and a mapping of dataset names to Dagster asset keys.
- At definition time, the component reads the check files and produces the correct set of asset check specs — one per check defined in the YAML files.
- At execution time, the component runs the Soda scan and maps each check result (pass/fail/warn) to a Dagster asset check result. A "pass" outcome should report as passed; "fail" and "warn" outcomes should report as not passed.
- If a check is declared in the YAML but no result comes back from the scan, that check should still produce a failed asset check result with a descriptive error message.
- If the scan itself throws an error (e.g., a database connection failure), all declared checks for that dataset should produce failed results with error details included in the result metadata.
- The component should support being loaded from a configuration file, with the dataset-to-asset-key mapping configurable via that file.
- The component should support scaffolding, generating a template check file and configuration so new users can get started quickly.
- Severity information from Soda checks should be reflected in result metadata; when severity is not present on a result, a configurable default severity should be used.

## Why This Matters

Data teams using both Soda and Dagster today have no first-class integration. They can't see their Soda check results in the Dagster UI as native asset checks without writing substantial custom code. A dedicated component that handles YAML parsing, scan execution, and result mapping makes this integration accessible with minimal configuration.
