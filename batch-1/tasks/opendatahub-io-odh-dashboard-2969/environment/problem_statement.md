## Description

The role binding generation logic and its associated type definitions are currently tightly coupled to the project-level resource sharing feature. This means the same reusable pattern cannot be shared with other parts of the application — such as model registry access management — without duplicating code or creating undesirable cross-feature dependencies.

We need to extract the shared types and the role binding generator into a common, general-purpose location so they can be reused across different features. Additionally, we need a new permissions management page for model registries so that platform administrators can control which users and groups have access to specific model registries directly from the settings UI.

## Expected Behavior

- The role binding permission types (subject kind and role kind) should be defined in a shared concepts location, not inside the project-sharing feature directory.
- The role binding generator function should be renamed to reflect its general-purpose nature and accept an additional parameter for the roleRef kind, enabling it to work for both project-level and registry-level scenarios.
- The UI components used for managing role binding permissions should use updated, generic data attributes (no longer referencing project-sharing specifically).
- A new dedicated permissions page for model registry settings should be available, allowing administrators to add, edit, and delete user and group access bindings for each model registry.
- The permissions page should be restricted to platform administrators — non-admins should see a not-found page.
- If a user navigates to the permissions page for a model registry that does not exist, they should be redirected back to the model registry settings list.

## Why This Matters

Without this change, adding permissions management to model registries would require duplicating the role binding infrastructure. Extracting the shared logic enables both project sharing and model registry permissions to use the same underlying mechanism, and gives platform administrators a proper UI for managing who can access which model registries.
