## Description

The RBAC permissions for the prometheus operator component are outdated and do not include resource types introduced in newer versions of the monitoring API. As a result, the prometheus operator may fail to function correctly when running in environments with up-to-date versions, because it lacks the necessary cluster permissions to manage certain resources.

## Expected Behavior

- The cluster role for the prometheus operator should include permissions for newly introduced monitoring resource types, including prometheus agents (and their sub-resources), scrape configurations, and status subresources for resources that have gained them (such as alertmanager and thanos ruler).
- The cluster role should also include read access to storage classes, which is needed by newer versions of the prometheus operator to manage persistent storage.
- The verb ordering for the rule governing services and endpoints should be consistent with other rules.

## Why This Matters

Without these updated permissions, deploying a newer version of the prometheus operator will result in authorization failures when it tries to access the API resources it depends on. Keeping the cluster role aligned with what the prometheus operator actually needs ensures it can be upgraded without permission-related errors.
