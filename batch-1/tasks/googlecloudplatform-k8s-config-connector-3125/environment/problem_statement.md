## Description

The BigQuery Dataset controller currently uses a full-replacement approach when updating datasets, sending the entire dataset object (including fields returned by the API that are read-only) back in every update request. This is incorrect because it sends fields the API does not accept for writes and may cause unintended side effects. The controller should instead send only the fields that are actually being changed.

Additionally, when deleting a dataset, the controller does not explicitly specify whether the dataset's contents should be removed. This ambiguity can lead to unexpected behavior depending on the API's implicit defaults.

## Expected Behavior

- Dataset update operations should use partial-update semantics, sending only the mutable fields (such as friendly name, labels, access policies, expiration settings, encryption configuration, and related options) and omitting read-only fields (like creation time, etag, and internal identifiers).
- Dataset deletion should explicitly request that dataset contents are preserved (not deleted), making the behavior unambiguous regardless of API defaults.
- After each successful reconciliation, the dataset's cloud resource identifier should be written back into the Kubernetes resource specification so users can reference it directly.
- The default collation field should be properly managed and reflected in both update requests and exported resource manifests.
- The resource status should report the external reference and observed location after reconciliation.

## Why This Matters

Sending read-only fields in update requests can cause API errors or lead to fields being reset unintentionally. Using explicit parameters in delete requests prevents accidental data loss or unexpected behavior. Writing the resource ID back to the spec gives users a complete and accurate view of the managed resource.

The test normalization infrastructure should also be simplified: the per-service normalization hook (which applied service-specific field replacements during HTTP log normalization) is no longer needed and should be removed. Callback functions used during HTTP log normalization should be simplified to not receive the request URL.
