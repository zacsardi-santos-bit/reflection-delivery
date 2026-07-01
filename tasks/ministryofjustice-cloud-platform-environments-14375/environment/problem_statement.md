## Description

We need to onboard a new development namespace for the Probation Workforce Vacancies application onto the live cloud platform cluster. The application team currently has no dedicated namespace, which means they cannot deploy or manage their service.

## Expected Behavior

- A new namespace directory should be created under namespaces/live.cloud-platform.service.justice.gov.uk/ for the data-platform-app-probation-recruitment-app-dev environment
- The namespace directory must include the standard namespace configuration file (00-namespace.yaml) as required by the platform

## Why This Matters

Without a registered namespace, the application team cannot deploy their service to the shared cluster. This is a standard platform onboarding requirement — every application must have its namespace declared in this repository before any resources can be provisioned for it.
