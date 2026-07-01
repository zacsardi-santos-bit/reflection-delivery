Simplify the environment model by replacing separate minimum and maximum instance count specifications with a single desired scale value. Introduce environment types to distinguish between static environments, which manage a fixed pool of instances, and dynamic environments, which only provision a cluster and security group without managing instances.

*   Update Models:
    *   Modify `CreateEnvironmentRequest` in `common/models/create_environment_request.go` to include `EnvironmentType` and `Scale` fields, removing `MinScale` and `MaxScale`.
    *   Modify `UpdateEnvironmentRequest` in `common/models/update_environment_request.go` to include a `Scale` pointer field, removing `MinScale` and `MaxScale`.
    *   Modify `Environment` in `common/models/environment.go` to include `EnvironmentType` and `DesiredScale`, removing `MinScale`.
    *   Modify `EnvironmentSummary` in `common/models/environment_summary.go` to include `EnvironmentType`.

*   Implement Environment Creation and Management:
    *   Ensure `createTags` in `api/provider/aws/` accepts `environmentType` and stores a tag with Key "type" and Value equal to `environmentType`.
    *   For static environments, store a tag with key 'type' and value 'static'.
    *   For dynamic environments, create only a security group and ECS cluster, and store tags for name, type ('dynamic'), and operating system.

*   Update Environment Reading and Updating:
    *   Populate `EnvironmentType` from the stored tag with key 'type' when reading environments.
    *   Set `CurrentScale` to the number of running container instances.
    *   Return `DesiredScale` instead of `MinScale` and `MaxScale` in the `Environment` model.
    *   Apply the single `Scale` value as both the minimum and maximum size of the Auto Scaling Group when updating environments.

*   Modify CLI Commands:
    *   In `cli/command/environment.go`, replace `--min-scale` and `--max-scale` with a single `--scale` flag. Reject negative values as input errors. Set `EnvironmentType` to 'static' when `--scale` is specified.
    *   Update `PrintEnvironments` in `cli/printer/` to display columns: ENVIRONMENT ID, ENVIRONMENT NAME, TYPE, OS, LINKS, removing SCALE and INSTANCE TYPE.
    *   Update `PrintEnvironmentSummaries` in `cli/printer/` to include a TYPE column.

*   Update Terraform Configurations:
    *   In `plugins/terraform/layer0/resource_layer0_environment.go`, replace `min_scale` and `max_scale` with `scale` and add `environment_type`.
    *   In `plugins/terraform/layer0/data_source_layer0_environment.go`, expose `scale` and `environment_type`, removing `min_scale`, `max_scale`, and `current_scale`.

*   Implement Terraform Resource Creation:
    *   In `resourceLayer0EnvironmentCreate`, read `environment_type` and `scale` from resource data and pass them in a `CreateEnvironmentRequest`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.