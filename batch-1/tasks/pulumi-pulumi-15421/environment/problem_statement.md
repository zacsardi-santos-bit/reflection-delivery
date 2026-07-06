## Description

When a Pulumi Node.js project lives inside a monorepo organized with npm or yarn workspaces, running the dependency installation command doesn't detect the workspace structure. Instead of installing from the workspace root, it installs only within the project subdirectory, which means workspace-linked local packages from sibling directories in the monorepo are not found or resolved correctly.

## Expected Behavior

- When a Pulumi project is part of an npm or yarn workspace, dependency installation should detect the workspace root and install from there, so that workspace-linked packages are available.
- When using yarn's nohoist feature in workspace configurations, installation should still succeed correctly.
- When a project is nested below a parent directory that has workspace configuration but does NOT list the project as a workspace member, installation should proceed normally from the project directory — the project should not be incorrectly treated as part of the workspace.
- When no workspace is detected at all, installation should continue as before without any errors.

## Why This Matters

Monorepos using npm or yarn workspaces are a common pattern for sharing code between multiple packages. Pulumi users who structure their infrastructure code as part of such a monorepo currently cannot successfully install dependencies if their program imports packages from sibling workspace members. Supporting workspace detection makes Pulumi usable in this common development setup.
