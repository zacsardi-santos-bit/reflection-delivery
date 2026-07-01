## Description

The AWS authentication manager currently does not enforce real access-control policies for DAG operations. When a user attempts to access a DAG, the system simply checks whether they are logged in, without consulting Amazon Verified Permissions. This means any authenticated user can access all DAGs regardless of what the policies say.

Additionally, the authorization facade does not handle the case where no user object is available — instead of gracefully returning "not authorized," it may encounter an error.

Finally, the authorization service needs to support a configurable AWS region, and there is no way to pass contextual information (such as which part of a DAG is being accessed) into the policy evaluation.

## Expected Behavior

- DAG authorization should be evaluated through Amazon Verified Permissions, just like other resource types (connections, variables, pools, etc.), rather than falling back to a simple login check.
- It must be possible to differentiate authorization at the level of DAG sub-entities — for example, whether a user can view a DAG's source code, its run history, or its task instances — by passing relevant context to the policy evaluation.
- When no user is provided to the authorization check, the system should return "not authorized" cleanly instead of raising an error.
- The authorization facade should support a configurable AWS region name, set via the auth manager configuration section.
- All authorization methods should correctly propagate the boolean decision returned by the policy service to their callers.

## Why This Matters

Without proper DAG-level authorization, access control policies defined in Amazon Verified Permissions are ignored for DAG operations, creating a security gap. Fixing this ensures that DAG access is governed by the same policy engine as all other Airflow resources, and that fine-grained DAG sub-resource access can be controlled.
