## Description

The service profile feature in this service mesh has a few issues that need to be fixed to make route and response configuration more intuitive and correct.

First, when a route condition specifies multiple matching criteria (for example, both an HTTP method and a URL path pattern), the current implementation silently ignores that route entirely instead of treating all the criteria as a combined requirement. This means valid configurations are being dropped without any warning, which is surprising and incorrect behavior.

Second, the field naming for classifying responses uses a confusing double-negative pattern. Currently, to mark a response class as a failure, you must set a field that means "is success" to a negative value — a double negative that is easy to misread. This should instead use a direct "is failure" field with positive semantics. The serialization key names for these fields should also be updated to reflect the new naming.

Third, service profiles are currently identified using a short name combining service and namespace, rather than the fully-qualified cluster-local DNS name. This should be updated to use the full DNS name for consistency.

Finally, the CLI tooling should support generating example service profile templates that use the updated field naming conventions, so users can see correct examples when creating new profiles.

## Expected Behavior

- Routes with multiple criteria in their conditions should be accepted and converted to combined "all must match" logic
- Response classification should use a clear "is failure" field instead of an "is not success" pattern
- Serialization keys for response classes and failure status should reflect the updated naming
- Service profile names should use the fully-qualified cluster-local DNS format
- CLI profile template generation should produce valid profiles with the updated field names and include a working example route with response class configuration

## Why This Matters

Silent route dropping makes it difficult to debug service profile configurations. The confusing field naming increases the chance of misconfiguration. Fixing these issues makes the service profile feature more correct, predictable, and user-friendly.
