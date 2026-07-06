Implement the necessary changes to a TypeScript type generation library to address issues with tuple structs and serialization-skip attributes. Ensure that tuple struct fields respect optional/nullable annotations and that serialization-skipping annotations correctly influence TypeScript output.

*   Update tuple struct code generation in `macros/src/types/tuple.rs`:
    *   Support per-element optionality annotations on tuple struct fields.
        *   `#[ts(optional)]` on `Option<T>` should render as `(T)?`.
        *   `#[ts(optional = nullable)]` on `Option<T>` should render as `(T | null)?`.
        *   `#[ts(optional = false)]` should render as `T | null`, overriding other settings.
    *   Implement struct-level `optional_fields` attribute:
        *   Render all `Option<T>` elements as `(T)?` unless overridden.
    *   Implement struct-level `optional_fields = nullable` attribute:
        *   Render all `Option<T>` elements as `(T | null)?` unless overridden.
*   Update field attribute parsing in `macros/src/attr/field.rs`:
    *   Recognize `serde` annotations when `serde-compat` feature is enabled:
        *   `#[serde(skip_serializing, default)]` or `#[serde(skip_serializing_if = "...", default)]` should infer optional (and nullable for `Option<T>`).
        *   `#[serde(skip_serializing_if = "...")]` without `default` should not infer optional.
*   Update struct-level attribute parsing in `macros/src/attr/struct_.rs`:
    *   Support `#[ts(optional_fields = false)]` to disable automatic serde inference.
*   Ensure explicit per-field overrides take precedence:
    *   `#[ts(optional = false)]` should render fields as non-optional.
    *   `#[ts(optional)]` should render fields as optional but not nullable.
*   Ensure that tuple struct elements with serialization-skip annotations are correctly rendered:
    *   When `serde-compat` is enabled, elements with `skip_serializing` and a default value should render as `(T | null)?`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.