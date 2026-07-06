Migrate the project's mock generation library from the archived version to the actively maintained community fork. Update all relevant import paths and ensure generated mock code adheres to modern Go type conventions.

*   Replace the module dependency:
    *   In `go.mod` and `go.sum`, replace `github.com/golang/mock` with `go.uber.org/mock`.
    *   Ensure test files can compile by importing `go.uber.org/mock/gomock`.

*   Update import paths in source files:
    *   Change all imports of `github.com/golang/mock/gomock` to `go.uber.org/mock/gomock`.
    *   Include generated mock files in directories such as `pkg/kubelet/apis/podresources/testing/`, `pkg/kubelet/cadvisor/testing/`, `pkg/kubelet/container/testing/`, etc.

*   Modify generated mock code:
    *   Update method parameters in non-test mock recorder types from `interface{}` to `any`.

*   Remove the legacy dependency:
    *   Ensure `github.com/golang/mock` is completely removed from `go.mod` so no source files depend on it.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.