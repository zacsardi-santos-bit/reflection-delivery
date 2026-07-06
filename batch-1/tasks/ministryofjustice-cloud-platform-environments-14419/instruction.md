Remove the AWS IAM role binding configuration for the activities-api-dev namespace by deleting the specified file. This will ensure that the unnecessary cloud infrastructure is no longer provisioned for this environment.

*   Delete the file located at:
    *   `namespaces/live.cloud-platform.service.justice.gov.uk/activities-api-dev/resources/irsa.tf`
        *   Ensure this file does not exist in the repository to confirm the removal of AWS IAM role bindings for service accounts in the activities-api-dev namespace.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.