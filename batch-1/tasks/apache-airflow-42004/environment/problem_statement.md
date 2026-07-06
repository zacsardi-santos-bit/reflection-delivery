## Description

Airflow currently lacks a lightweight, built-in authentication mechanism for the web interface. Users who want a simple development or testing environment must configure a full external authentication provider, which is unnecessarily complex for non-production use. We need a minimal, self-contained auth manager that ships with Airflow and works out of the box.

## Expected Behavior

- A new, simple authentication manager should be available that can be selected via configuration.
- Administrators declare user accounts with an associated role (viewer, user, operator, or admin), and the system automatically generates passwords and writes them to a local file.
- The web interface should display a login page with a heading ("Sign in"), a short description ("Enter your login and password below:"), and form fields for username and password.
- The logout endpoint should clear the session and redirect users back to the login page.
- The login submission endpoint should validate credentials against the stored passwords: on success, redirect to the main Airflow dashboard; on failure, redirect back to the login page with an error indicator.
- Role-based access rules must be enforced:
  - The **admin** role grants full access to everything.
  - The **op** role grants read and write access to operational resources (configuration, connections, datasets, pools, variables), plus everything the lower tiers can do.
  - The **user** role grants full access to DAGs (including writes), plus read access to datasets and pools.
  - The **viewer** role grants read-only access to DAGs, datasets, and pools, plus access to UI views.
  - Any logged-in user can access UI views and custom views, regardless of role.
  - Unauthenticated requests are denied for all resource types.

## Why This Matters

This makes Airflow significantly easier to set up and evaluate for developers and small teams who don't want to manage an external identity provider just to run the scheduler and inspect their DAGs.
