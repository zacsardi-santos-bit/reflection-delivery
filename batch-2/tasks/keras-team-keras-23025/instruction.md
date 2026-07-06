Implement properties to standardize data shard assignment and model replica reporting in the distributed training library. Update the naming of process count attributes for consistency.

*   Update the Distribution base class:
    *   Expose a public property `num_processes` that returns the total number of processes in the distributed computation.
        *   Replace the internal `_num_process` attribute with `_num_processes`.
    *   Expose a property `data_shard_id` to compute the data shard index for the current process.
        *   When `num_model_replicas >= num_processes`, return the current process's ID.
        *   When `num_model_replicas < num_processes`, return `_process_id // (num_processes // num_model_replicas)`.

*   Update the DataParallel subclass:
    *   Expose a `num_model_replicas` property that returns the total number of devices in the device mesh.

*   Update the ModelParallel subclass:
    *   Expose a `num_model_replicas` property that returns the size of the batch/data dimension in the device mesh.
    *   Change the validation error message to: '`num_processes` must be divisible by `num_model_replicas`'.

*   Ensure the `num_processes` property coexists with the `_num_processes` attribute.
    *   Update any code that previously used `_num_process` to use `_num_processes`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.