## Description

When a project configures its package manager requirement with a lenient "ignore failures" policy, the intent is that the tool should continue working even if the exact package manager version doesn't match. However, despite this lenient policy, the system still records the resolved package manager version into the lockfile under the package manager dependencies section. This is inconsistent — if a mismatch is acceptable and the system should just proceed without enforcing the constraint, there is no reason to pin or track the package manager in the lockfile.

## Expected Behavior

- When the package manager policy is set to ignore failures, the lockfile should **not** include a package manager dependencies entry after a self-update or version sync.
- The logic that decides whether to persist package manager information to the lockfile should respect the "ignore" failure policy and skip persistence in that case.
- This persistence gate logic should live in the shared configuration reader package so it can be reused across the codebase.

## Current Behavior

The system always writes the resolved package manager version to the lockfile, even when the project's package manager policy is configured to silently ignore mismatches. This results in unnecessary lockfile churn and contradicts the non-enforcing intent of the policy.

## Why This Matters

Teams that deliberately opt into a lenient package manager policy (accepting any compatible version rather than enforcing a strict match) should not see their lockfile updated with a pinned package manager entry. The lockfile change is surprising and unwanted in contexts where the policy is intentionally permissive.
