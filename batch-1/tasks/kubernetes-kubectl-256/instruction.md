Implement a safe mechanism to manage configmap entries in manifest files and refactor the manifest file I/O operations to support a pluggable filesystem. Ensure configmaps are updated without duplication and data sources are merged correctly. Enable in-memory testing by abstracting file operations.

*   Implement the `getOrCreateConfigMap` function in `pkg/kinflate/commands/configmap.go`:
    *   Accept a `Manifest` pointer and a configmap name string.
    *   Return a pointer to an existing `ConfigMap` in the manifest's `Configmaps` slice if found.
    *   Append a new `ConfigMap` with the given name if not found and return a pointer to it.
    *   Ensure the function never returns nil.

*   Implement the `mergeData` function in `pkg/kinflate/commands/configmap.go`:
    *   Accept a `DataSources` pointer and a `dataConfig` value.
    *   Append `LiteralSources` from `dataConfig` to the `DataSources` `LiteralSources` field.
    *   Append `FileSources` from `dataConfig` to the `DataSources` `FileSources` field.
    *   Set `DataSources` `EnvSource` to `dataConfig` `EnvFileSource`.
    *   Return a non-nil error if `EnvSource` is already set to a different non-empty value and `EnvFileSource` is also non-empty.

*   Ensure `DataSources` type in `pkg/apis/manifest/v1alpha1/types.go`:
    *   Include fields: `LiteralSources []string`, `FileSources []string`, `EnvSource string`.
    *   Ensure `ConfigMap` struct includes a `DataSources` field.

*   Define `ManifestLoader` struct in `pkg/kinflate/util/manifestloader.go`:
    *   Include an exported `FS` field of a filesystem interface type.
    *   Implement `Write` method:
        *   Accept a filename string and a `Manifest` pointer.
        *   Serialize the manifest and write it to the filesystem.
        *   Return a non-nil error when writing to an empty filename.
    *   Implement `Read` method:
        *   Accept a filename string.
        *   Read and deserialize the manifest from the filesystem.
        *   Return a `Manifest` pointer and any error.
        *   Ensure a manifest written via `Write` and read via `Read` is deeply equal to the original.

*   Ensure `ManifestLoader` supports a fake/in-memory filesystem:
    *   When constructed with a fake FS instance, `Write` followed by `Read` on the same filename must round-trip the manifest correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.