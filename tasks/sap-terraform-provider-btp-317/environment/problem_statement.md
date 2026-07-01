## Description

The subaccount environment instance resource currently does not support importing existing instances into Terraform state. This makes it impossible to bring environment instances that were created outside of Terraform (or in another workspace) under Terraform management without destroying and recreating them.

Additionally, the existing import identifier parsing has a bug: it references the wrong attribute name when restoring subaccount information during an import, causing the import to fail even if someone tried to use it. The error message for malformed import identifiers also references the wrong attribute name.

## Expected Behavior

- Users should be able to import an existing subaccount environment instance into Terraform state by providing a composite identifier consisting of the subaccount ID and the environment instance ID, separated by a comma.
- After importing, the Terraform state should match the state that would result from a normal apply — no spurious diffs should appear on the next plan.
- When importing, any internally managed fields in the parameters (such as runtime status fields not settable by the user) should be stripped from the imported state, to keep it consistent with what Terraform would have recorded had it created the resource itself.
- If the import identifier is malformed (not exactly two non-empty comma-separated parts), a clear error message should be returned indicating the expected format of subaccount ID followed by environment instance ID, separated by a comma.

## Why This Matters

Without import support, organizations that have existing environment instances — whether created via the SAP BTP cockpit, CLI, or another tool — cannot bring them under Terraform management. This forces unnecessary recreation of resources. Having proper import support is essential for adopting Terraform incrementally in environments that already have infrastructure provisioned by other means.
