## Description

The go-github library wraps GitHub's REST API, but it is currently missing support for managing security manager teams within an organization. GitHub provides API endpoints to list, add, and remove the teams that hold the "security manager" role for an organization (teams that can view security alerts and manage security settings across all repositories). Without these bindings, developers cannot use this library to manage security manager team assignments.

## Expected Behavior

- Developers should be able to list all teams that currently have the security manager role in a given organization.
- Developers should be able to grant the security manager role to a specific team within an organization.
- Developers should be able to revoke the security manager role from a specific team within an organization.
- All three operations should return the standard HTTP response information and any errors, consistent with existing patterns in the library.

## Why This Matters

Many organizations manage security policies programmatically. Without these API bindings, developers must resort to direct HTTP calls outside of this library to perform security manager team management, which is inconsistent with how other organization management tasks are handled. Adding these methods brings the library up to parity with the GitHub API for this security management feature.
