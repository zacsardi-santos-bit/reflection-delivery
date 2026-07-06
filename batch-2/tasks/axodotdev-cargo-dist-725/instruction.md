Update the release distribution tool to include a boolean field in the manifest output that indicates whether the release tag was explicitly specified by the user or automatically inferred. This will help downstream consumers distinguish between real releases and dry-run scenarios.

*   Modify the `DistGraphBuilder::new` function:
    *   Add a new sixth boolean parameter named `announcement_tag_is_implicit` after the `allow_all_dirty` parameter.
    *   Update all existing call sites to pass an appropriate value for this parameter.
    *   Ensure the parameter is used to set the corresponding field in the `DistManifest`.

*   Update the `DistManifest` struct:
    *   Add a new boolean field named `announcement_tag_is_implicit`.
    *   Set the default value of this field to `false`.
    *   Ensure this field is serialized as the JSON key "announcement_tag_is_implicit".
    *   Position this field immediately after the "announcement_tag" field in the JSON output.

*   Implement logic to set the `announcement_tag_is_implicit` field:
    *   Set this field to `true` when the announcement tag is inferred automatically (i.e., not explicitly provided by the user).
    *   Set this field to `false` when the tag is explicitly specified by the user.

*   Ensure the `announcement_tag_is_implicit` value is correctly propagated:
    *   Pass the value from `DistGraphBuilder::new` to the `DistManifest` so it appears correctly in the plan/manifest JSON output.

*   Ensure the `DistManifest` default constructor initializes `announcement_tag_is_implicit` to `false`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.