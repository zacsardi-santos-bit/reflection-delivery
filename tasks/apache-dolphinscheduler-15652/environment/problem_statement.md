## Description

The resources management service has several methods whose names don't clearly describe what they actually do, leading to confusion when reading or maintaining the code. Additionally, there are some API endpoints and service methods that are no longer needed and should be removed to keep the codebase clean.

There is also a security gap in the resource content update operation: it is currently possible to specify an arbitrary file path that falls outside of the expected storage directory for a given tenant, which could be exploited for unauthorized file access.

## Expected Behavior

- The method responsible for uploading a resource file should be renamed to more accurately reflect that it is an upload operation.
- The method responsible for creating a resource file from inline text content should be renamed to better reflect that it creates a resource file (as opposed to uploading one).
- The corresponding REST controller methods and service interface definitions should be updated to use the new names consistently.
- The resource content update operation should validate that the specified resource path falls within the expected storage directory for the given tenant. If the path is outside that directory, the operation should be rejected with an appropriate error message indicating the path is illegal.
- Two service methods and their corresponding controller endpoints related to managing UDF function authorization for specific users should be removed, as this functionality is no longer needed in the resources service.

## Why This Matters

Consistent naming makes the API easier to understand and reduces bugs from confusion about which method to call. The path validation in content updates is a meaningful security improvement that prevents requests from targeting files outside the tenant's designated storage area.
