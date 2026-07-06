## Description

When Flutter's local engine tooling is unable to find a valid engine build directory at the configured path, the error message it displays is vague and unhelpful. The current phrasing doesn't make it immediately clear what path was searched or precisely what was missing, making it harder for developers to quickly diagnose the problem.

## Expected Behavior

When the tool fails to locate a Flutter engine build directory at the specified path, the error message should clearly state that no engine build directory was found at that location, using more precise and actionable language that directly identifies both the failure and the exact path involved.

## Current Behavior

The current error message uses vague, passive phrasing that does not directly state the nature of the failure nor clearly identify the path that was searched, making it less immediately scannable for developers troubleshooting their local engine setup.

## Why This Matters

Developers working with a locally built Flutter engine frequently encounter this error when their environment is misconfigured. A clearer, more direct error message helps them immediately understand what was searched for and where, reducing confusion and speeding up diagnosis when setting up or troubleshooting a local engine environment.
