## Description

The prow deck dashboard has a job rerun endpoint that currently has no access control. Any user who can reach the endpoint can trigger a completely new CI job run, with the only protection being an obscure job identifier. This is unsafe for instances where only certain team members should be able to trigger new jobs.

## Expected Behavior

- The rerun endpoint should support a read-only mode that returns the job specification without creating a new job, responding to the appropriate HTTP method.
- The rerun endpoint should support a creation mode that only accepts the correct HTTP method and checks whether the requesting user is authorized before creating a new job.
- Authorization should be configurable: either allow any authenticated GitHub user to trigger jobs, or restrict to an explicit allowlist of GitHub usernames.
- If a write request is sent but the creation feature is disabled, the server should respond with a clear method rejection rather than silently ignoring it.
- If an authenticated user is not on the allowlist and the "allow anyone" flag is not set, the server should not create the job.
- A utility should be available to retrieve the currently authenticated GitHub user's identity from the OAuth session, so that authorization checks can be performed inside request handlers.

## Why This Matters

Without access control, anyone who can access the deck UI can create new CI jobs, which wastes resources, can be abused, and is inconsistent with typical security expectations for CI infrastructure. By gating job creation on GitHub identity and an allowlist, operators can control who is permitted to trigger reruns.
