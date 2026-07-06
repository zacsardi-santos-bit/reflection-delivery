I'm running into an issue with my private npm registry.

*   The merge_manifest function must accept four parameters in this order: existing (Option<&Value>), incoming (&Value), hosted (Option<&Value>), and now_iso (&str). The hosted parameter identifies the locally-hosted packument, distinct from the existing (pre-existing or upstream) packument.

*   When hosted is Some and an incoming version entry already exists in the hosted packument's versions map, merge_manifest must preserve the hosted version's data verbatim — including dist.integrity and all dependency fields. Only the deprecated field may differ: if the incoming entry includes deprecated, that value replaces the hosted version's deprecated field; if the incoming entry omits deprecated, the deprecated key must be removed from the hosted version.

*   When hosted is Some and the incoming entry for an already-hosted version is a non-object value (e.g. null), the malformed entry must be ignored and the hosted version must be preserved unchanged in the merged result.

*   When hosted is None, incoming version data takes precedence for all versions, including versions that may exist in the existing packument. This covers the case where existing is an upstream-only packument, so its versions are not considered immutable.

*   An HTTP PUT request that republishes an already-hosted version with the same dist.integrity but no attachment (a metadata-only update) must be accepted with HTTP 201 CREATED. The deprecated field from the request body must be applied to the stored version.

*   An HTTP PUT request that attempts to change resolution-relevant fields (such as dependencies) of an already-hosted version while keeping dist.integrity unchanged and sending no attachment must be accepted with HTTP 201 CREATED, but the changed fields must not be written — the hosted version's metadata remains immutable except for deprecated.

*   An HTTP PUT request that sends a null (or other non-object) value for an already-hosted version entry must be accepted with HTTP 201 CREATED, and the hosted version must remain intact with its dist.integrity preserved.

*   An HTTP PUT request that tries to add a brand-new version entry without supplying a corresponding tarball attachment must be rejected with HTTP 400 BAD_REQUEST, and the new version must not appear in the stored packument.


*   Interface details: Type: Function
Name: merge_manifest
Location: pnpr/crates/pnpr/src/publish.rs
Signature: merge_manifest(existing: Option<&Value>, incoming: &Value, hosted: Option<&Value>, now_iso: &str) -> Value
Description: Merges an incoming publish packument with the existing stored packument. The new `hosted` parameter (third argument) carries the locally-hosted packument — versions found in `hosted` are treated as immutable except for their `deprecated` flag. When `hosted` is None, incoming version data is used as-is. The `now_iso` parameter is the current ISO timestamp string. This is a public function.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.