Consolidate the pod-building logic in the Tekton pipeline by moving the main pod assembly function and related helpers into a single package. Ensure that internal helpers remain private and that the public API is minimal and clean.

* Move the `MakePod` function:
    * Relocate from `pkg/reconciler/taskrun/resources` to `pkg/pod/pod.go`.
    * Ensure it remains exported with the signature: `MakePod(images pipeline.Images, taskRun *v1alpha1.TaskRun, taskSpec v1alpha1.TaskSpec, kubeclient kubernetes.Interface, entrypointCache EntrypointCache) (*corev1.Pod, error)`.
    * Accept a `pipeline.Images` struct as its first argument.

* Relocate constants for pod identification:
    * Move `ManagedByLabelKey` and `ManagedByLabelValue` to be exported from the `pkg/pod` package.

* Unexport helper functions and variables:
    * Change the following functions to be unexported in their respective files:
        * `credsInit` in `pkg/pod/creds_init.go`.
        * `resolveEntrypoints` in `pkg/pod/entrypoint_lookup.go`.
        * `orderContainers` in `pkg/pod/entrypoint.go`.
        * `convertScripts` in `pkg/pod/script.go`.
        * `workingDirInit` in `pkg/pod/workingdir_init.go`.
    * Rename and unexport the following variables and constants in `pkg/pod/entrypoint.go`:
        * `ToolsMount` to `toolsMount`.
        * `ToolsVolume` to `toolsVolume`.
        * `DownwardMount` to `downwardMount`.
        * `DownwardVolume` to `downwardVolume`.
        * `ReadyAnnotation` to `readyAnnotation`.
        * `ReadyAnnotationValue` to `readyAnnotationValue`.
        * `StepPrefix` to `stepPrefix`.
        * `SidecarPrefix` to `sidecarPrefix`.
    * Rename and unexport the following in `pkg/pod/script.go`:
        * `ScriptsVolumeMount` to `scriptsVolumeMount`.
        * `ScriptsVolume` to `scriptsVolume`.

* Update the `pipeline.Images` struct:
    * Ensure it includes fields: `EntrypointImage`, `CredsImage`, and `ShellImage` (all string type).

* Move and update the test file:
    * Relocate `pkg/reconciler/taskrun/resources/pod_test.go` to `pkg/pod/pod_test.go`.
    * Ensure it uses the `pod` package to access unexported symbols directly.
    * Define a test variable `images` of type `pipeline.Images` with specific image values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.