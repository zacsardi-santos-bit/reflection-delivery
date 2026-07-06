We're dropping LoongArch64 on Linux from our officially supported platform list.

*   The CI/CD pipeline platform matrix configuration file at eng/pipelines/common/platform-matrix.yml must not contain any reference to linux_loongarch64.

*   The CI/CD pipeline platform matrix multi-job configuration file at eng/pipelines/common/platform-matrix-multijob.yml must not contain any reference to linux_loongarch64.

*   The Docker workflow documentation file at docs/workflow/using-docker.md must not contain any reference to loongarch64.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.