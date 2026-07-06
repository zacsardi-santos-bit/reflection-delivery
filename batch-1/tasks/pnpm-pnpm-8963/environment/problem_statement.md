## Description

When pnpm installs packages, it automatically suppresses build scripts for dependencies that have not been explicitly approved. This is a security mechanism, but it leaves users without any easy way to audit which packages had their builds silently skipped. Additionally, developers can configure packages to explicitly skip their builds in project settings, but there is likewise no dedicated command to inspect that list.

There should be a new command that displays two categories of ignored package builds:

1. **Automatically ignored during installation** — packages whose build scripts were silently suppressed by the package manager during the last install.
2. **Explicitly configured to skip** — packages listed in the project configuration as builds to ignore.

## Expected Behavior

- When there are automatically ignored builds, each package name should be listed, along with guidance on how to approve or permanently ignore their builds.
- When there are no automatically ignored builds, the command should clearly indicate that (e.g., "None").
- When no installation has been performed yet (no node_modules directory), the command should indicate that it cannot determine the automatically-ignored builds.
- Packages explicitly configured to skip builds in the project settings should appear in a separate section.

## Why This Matters

Without this visibility, developers can't easily tell if important setup steps for their dependencies were silently skipped during installation. This makes it hard to diagnose mysterious issues caused by missing build artifacts, and there's no guided way to approve or permanently ignore specific package builds.
