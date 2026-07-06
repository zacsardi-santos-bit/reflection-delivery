Implement support for plugins to store and retrieve configuration data in the kubebuilder project's config file. Ensure compatibility with newer config versions and handle errors for unsupported versions.

*   Update `internal/config`:
    *   Create a `Config` struct embedding `config.Config` from `sigs.k8s.io/kubebuilder/pkg/model/config`.
        *   Include unexported fields `path string` and `fs afero.Fs` (named `fs`).
    *   Define a `DefaultPath` constant for the default config file path.
    *   Implement the `Save()` method:
        *   Return an error if `path` is empty.
        *   Write a YAML representation to `fs` at `path`.
        *   Omit `ExtraFields` for `Version1` configs.
        *   Serialize `ExtraFields` under `plugins:` for `Version2` configs.
    *   Implement `readFrom(fs afero.Fs, path string) (config.Config, error)`:
        *   Parse the YAML config file from the specified path.
        *   Store `plugins:` data in `ExtraFields` for `Version2` configs.
        *   Set `ExtraFields` to nil for `Version1` configs.
        *   Decode list values in `ExtraFields` as `[]interface{}`.

*   Update `pkg/model/config`:
    *   Create a `Config` struct with fields: `Version`, `Repo`, `Domain`, and `ExtraFields map[string]interface{}`.
        *   Use the YAML/JSON key `plugins` for `ExtraFields`.
    *   Implement `EncodeExtraFields(key string, extraFieldsObj interface{}) error`:
        *   Return an error for `Version1` configs.
        *   Marshal `extraFieldsObj` and store it in `ExtraFields[key]` for `Version2`.
    *   Implement `DecodeExtraFields(key string, extraFieldsObj interface{}) error`:
        *   Return an error for `Version1` configs.
        *   Decode `ExtraFields[key]` into `extraFieldsObj` for `Version2`.
        *   Return nil if `ExtraFields` is empty.
    *   Implement `Marshal() ([]byte, error)`:
        *   Serialize core fields first, excluding `ExtraFields`.
        *   Append `ExtraFields` under `plugins:` for non-Version1 configs.
        *   Produce empty bytes for an all-zero-value config.
    *   Implement `Unmarshal(in []byte, out *Config) error`:
        *   Deserialize YAML bytes into the `Config`.
        *   Set `ExtraFields` to nil for `Version1` configs.

*   Define constants:
    *   `Version1` for config version 1, where `ExtraFields` are unsupported.
    *   `Version2` for config version 2, supporting `ExtraFields`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.