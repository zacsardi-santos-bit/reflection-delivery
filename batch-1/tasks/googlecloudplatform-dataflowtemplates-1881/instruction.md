Implement a new named group for Kafka-related modules in the CI/CD build system to allow targeted CI jobs for Kafka-specific code. Update the module selection logic to recognize this new group and adjust the default exclusion list logic to handle shared library modules correctly.

Requirements:

*   Update the `ModulesToBuild` function:
    *   When the module selector is set to 'KAFKA', return the following module paths: 'v2/kafka-common/', 'v2/kafka-to-bigquery/', 'v2/kafka-to-gcs/', 'v2/kafka-to-kafka/', 'v2/kafka-to-pubsub/', and 'plugins/templates-maven-plugin'.
    *   When the module selector is 'DEFAULT', exclude modules whose paths contain 'common/' and those starting with 'plugins/' from the returned exclusion list. Return negated paths for all other non-ALL, non-DEFAULT module entries.

*   Add a constant in `cicd/internal/flags/common-flags.go`:
    *   Export a constant named `KAFKA` with the string value "KAFKA" alongside existing constants ALL, DEFAULT, and SPANNER.

*   Update the `moduleMap` variable in `cicd/internal/flags/common-flags.go`:
    *   Add a 'KAFKA' key with the value `[]string{"v2/kafka-common/", "v2/kafka-to-bigquery/", "v2/kafka-to-gcs/", "v2/kafka-to-kafka/", "v2/kafka-to-pubsub/", "plugins/templates-maven-plugin"}`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.