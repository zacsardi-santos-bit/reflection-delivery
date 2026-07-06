Update the validation logic for policy names to ensure that error messages for names exceeding 63 bytes use standardized Kubernetes phrasing. This update applies to all policy types and supported API versions.

*   Implement validation for Policy and ClusterPolicy names:
    *   Return an error with the Detail field set to "may not be more than 63 bytes" and error type ErrorTypeTooLong when the name exceeds 63 bytes.
    *   Ensure the full error string is "name: Too long: may not be more than 63 bytes".
*   Implement validation for CleanupPolicy and ClusterCleanupPolicy names:
    *   Return an error with the Detail field set to "may not be more than 63 bytes" and error type ErrorTypeTooLong when the name exceeds 63 bytes.
    *   Ensure the full error string is "metadata.name: Too long: may not be more than 63 bytes".
*   Apply these validation error message requirements consistently across all API versions: v1, v2, and v2beta1.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.