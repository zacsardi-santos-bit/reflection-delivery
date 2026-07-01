Reorganize the out-of-band resource manager component by moving it to a top-level package path. Ensure all sub-components are relocated together and maintain their functionality. Update the agent startup code to reference the new package location.

*   Move all source files from `pkg/agent/resourcemanager/outofband/` to `pkg/agent/orm/`.
    *   Change the package declaration in these files to `package orm`.
*   Relocate sub-packages:
    *   Move checkpoint files to `pkg/agent/orm/checkpoint/` with `package checkpoint`.
    *   Move endpoint files to `pkg/agent/orm/endpoint/` with `package endpoint`.
    *   Move executor files to `pkg/agent/orm/executor/` with `package executor`.
    *   Move metamanager files to `pkg/agent/orm/metamanager/` with `package metamanager`.
*   Update all internal import paths:
    *   Change references from `github.com/kubewharf/katalyst-core/pkg/agent/resourcemanager/outofband/...` to `github.com/kubewharf/katalyst-core/pkg/agent/orm/...`.
*   Modify the agent entry point:
    *   In `cmd/katalyst-agent/app/agent/orm.go`, import the manager from `github.com/kubewharf/katalyst-core/pkg/agent/orm`.
    *   Ensure the function call is `orm.NewManager` instead of `outofband.NewManager`.
*   Maintain functionality:
    *   Ensure checkpoint unmarshaling, endpoint lifecycle management, executor operations, resource mapping, container logic, pod resource handling, and checkpoint state continue to function correctly under the new package paths.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.