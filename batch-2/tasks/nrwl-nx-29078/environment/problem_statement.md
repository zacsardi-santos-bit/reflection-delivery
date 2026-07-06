## Description

In a large monorepo with many publishable packages, it's easy for individual projects to drift from the organization's package.json standards over time. Without an automated check, common problems can slip through unnoticed: packages missing a name, packages scoped to the wrong organization, public packages missing the required publish access setting, projects that have executor or generator manifests but forget to declare them in their package.json, and packages that lack an exports declaration.

We need a workspace-level conformance rule that validates each project's package.json and reports violations when these standards are not met.

## Expected Behavior

The validation logic should check each project package.json for the following:

- The package must have a name field that is a string
- If the package name is organization-scoped, it must be scoped to the designated organization namespace — any other scope should be flagged
- Public packages must have their publish access explicitly set to public in their publish configuration
- If the project directory contains an executor manifest file, the package.json must correctly reference it in the appropriate field
- If the project directory contains a generator manifest file, the package.json must correctly reference it in the appropriate field
- The package.json must declare a non-empty exports map

Packages marked as private should be exempt from all of these checks.

Each detected problem should be reported as a violation including the path to the offending file, a clear human-readable message, and the name of the source project.

## Why This Matters

Without this automated check, inconsistencies in package.json files across the monorepo are only caught during manual code review or — worse — at publish time. An automated rule surfaces these issues early and consistently across all projects.
