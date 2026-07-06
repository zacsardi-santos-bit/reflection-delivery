## Description

Access control for pipeline runs is incomplete. Currently, authorization is only enforced when a run is first created — there are no permission checks when a user tries to archive, unarchive, delete, terminate, or retry an existing run. This means any authenticated user can modify or delete runs that belong to namespaces they should not have access to.

Additionally, runs stored in the database use an internal data model to track their namespace association, but there is no utility to extract the namespace from that model. The existing namespace-extraction helper only works on API-layer objects, not on stored model objects. This gap makes it impossible to perform authorization checks on existing runs using their stored resource references.

## Expected Behavior

- A utility function should exist to extract namespace information from internally stored run records (model-layer resource references), analogous to the existing utility for API-layer resource references.
- The existing API-layer authorization helper should be renamed to clearly distinguish it from the new model-layer one.
- Before processing any request to archive, unarchive, delete, terminate, or retry an existing run, the system should verify that the requesting user is authorized to access the namespace associated with that run.
- Unauthorized requests should be rejected with an appropriate error.
- In single-user deployments, these authorization checks should be skipped.

## Why This Matters

Without these checks, namespace isolation in multi-user deployments is broken — users can interfere with runs in namespaces they do not own. Closing this gap is necessary for correct multi-tenancy support.
