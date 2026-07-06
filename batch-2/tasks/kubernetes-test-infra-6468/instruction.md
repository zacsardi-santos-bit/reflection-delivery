Add the benchmark variant of the CRI-containerd node end-to-end job to the central job configuration registry. Ensure it is properly integrated with existing job variants and passes all configuration checks.

*   Update the `jobs/config.json` file:
    *   Add a new top-level entry with the key `'ci-cri-containerd-node-e2e-benchmark'`.
    *   Ensure this entry is formatted consistently with existing job entries.

*   Ensure the benchmark job entry:
    *   Coexists correctly with existing CRI-containerd node e2e jobs: `'ci-cri-containerd-node-e2e'`, `'ci-cri-containerd-node-e2e-serial'`, and `'ci-cri-containerd-node-e2e-flaky'`.
    *   Shares the same cloud infrastructure project as its sibling jobs if it uses a GCP project matching the pattern `'cri-containerd-node-e2e-*'`.

*   Verify that the configuration health check passes:
    *   Confirm that no orphaned environment files are detected.
    *   Ensure that the project-uniqueness validation does not reject the benchmark job.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.