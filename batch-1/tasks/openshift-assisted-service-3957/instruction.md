Implement the functionality to allow the image service container to know its own externally accessible URL by setting specific environment variables. Ensure these variables are populated based on the Route resource in OpenShift.

*   Add an environment variable named `IMAGE_SERVICE_SCHEME` to the image service StatefulSet container with the value 'https'.
*   Add an environment variable named `IMAGE_SERVICE_HOST` to the image service StatefulSet container.
    *   Set its value to match the host field of the OpenShift Route configured for the image service (e.g., 'my.test.images').
*   Ensure the scheme and host values are read from the externally-visible Route for the image service during the reconciliation process.
    *   If the Route cannot be found or its URL cannot be parsed, log a warning and set these variables to empty strings instead of failing.
*   When creating or updating the image service StatefulSet, ensure the `IMAGE_SERVICE_SCHEME` and `IMAGE_SERVICE_HOST` environment variables are included alongside existing environment variables.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.