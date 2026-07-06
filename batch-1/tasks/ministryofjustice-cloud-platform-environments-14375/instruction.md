Create a new namespace for the Probation Workforce Vacancies application on the live cloud platform cluster. Ensure the namespace is correctly configured and follows the platform's standard requirements.

*   Create a new directory for the namespace:
    *   Path: `namespaces/live.cloud-platform.service.justice.gov.uk/data-platform-app-probation-recruitment-app-dev/`
*   Include the standard namespace configuration file:
    *   File name: `00-namespace.yaml`
    *   Location: `namespaces/live.cloud-platform.service.justice.gov.uk/data-platform-app-probation-recruitment-app-dev/00-namespace.yaml`
    *   Ensure the file is a valid Kubernetes namespace configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.