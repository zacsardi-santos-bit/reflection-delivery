Update the Kubernetes cluster configuration files to support version 1.29. Add machine image entries for the new version and remove outdated feature gate configurations.

*   Modify `cluster/config-defaults.yaml`:
    *   Add configuration entries for Kubernetes v1.29 node images using the key `kuberuntu_image_v1_29`.
    *   Ensure the entry `cronjob_time_zone_enabled` is removed, as the CronJobTimeZone feature gate is now stable and does not require explicit configuration.
*   Update `cluster/node-pools/master-default/stack.yaml`:
    *   Reference Kubernetes v1.29 node images by including `kuberuntu_image_v1_29`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.