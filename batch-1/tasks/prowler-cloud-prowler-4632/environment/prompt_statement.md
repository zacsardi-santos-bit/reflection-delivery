I'm working on adding a new security check to Prowler for AWS CodeBuild projects. The check should detect when a project has sensitive credentials — like cloud access keys — stored directly as plaintext environment variables. Variables stored via a secure parameter store should be considered safe and never flagged.

The check should iterate over all CodeBuild projects and return one finding per project. Projects with no environment variables, or where all plaintext variables are non-sensitive or excluded, should get a passing result. Projects that have at least one plaintext variable containing a detectable secret should fail. The failure message needs to include the detected secret type and the variable name for each problematic variable, listing multiple findings as a comma-separated list.

I also need to support a configurable exclusion list: users should be able to specify environment variable names to skip during scanning. This allows suppressing known false positives without disabling the entire check.

On the data model side, the CodeBuild project model needs to be updated to include the list of environment variables (each with a name, value, and type), and the configuration file should be updated to include the new exclusion list setting (defaulting to empty).
