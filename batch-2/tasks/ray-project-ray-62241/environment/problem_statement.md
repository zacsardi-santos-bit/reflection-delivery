## Description

The LLM module currently lacks a formal, validated data model for specifying resource bundles and placement group configurations. When setting up distributed workloads — especially those using custom hardware accelerators — the system can implicitly add GPU resources to placement bundles even when the user did not request them. This leads to incorrect resource allocation for hardware like TPUs or other non-GPU accelerators, where GPU should default to zero unless explicitly specified.

## Expected Behavior

- There should be a structured configuration model for resource bundles that supports standard CPU and GPU fields (defaulting to zero) as well as arbitrary additional resource types.
- CPU and GPU values should always be stored and returned as floats, even when integers are supplied.
- Arbitrary additional resources should be accepted but validated: negative values should be rejected, and non-numeric values should be rejected with clear error messages.
- There should be a placement group configuration model that accepts either a list of bundles or a single per-worker bundle specification — but not both, and not neither. Both cases should produce clear validation errors.
- The placement group strategy should be validated against an allowed set of values, with invalid strategies rejected.
- Raw dictionary input for bundle fields should be automatically coerced into the proper bundle model type.

## Why This Matters

Without this structured model, resource requirements for distributed placement groups are error-prone and inconsistent. Custom accelerator workloads silently received unwanted GPU resource requests. Providing a validated, explicit configuration model makes resource allocation correct and predictable across all accelerator types.
