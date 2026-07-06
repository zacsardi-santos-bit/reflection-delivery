## Description

When fetching a specific, fully-qualified branch from a remote repository, the system currently queries the server for all refs under the parent directory of that branch instead of targeting the specific ref. This means fetching one particular branch causes the server to advertise every branch in the same namespace, including unrelated ones. This is both inefficient and produces misleading error messages.

## Problem

When you try to fetch a ref that does not exist on the remote (e.g., a branch that has been deleted or never existed), the error message reports the count of unrelated refs found under the same parent directory — not the count of refs that actually match your request. So instead of accurately reporting zero refs matched, you see a non-zero count from sibling refs in the same namespace.

Additionally, when a ref name has only a single path component after the root refs directory, the system currently returns no prefix at all instead of treating the ref as an exact target.

## Expected Behavior

- When fetching with an exact, non-wildcard refspec, the system should use the full ref name as the server-side filter. Only the requested ref (or refs sharing that exact prefix) should be advertised.
- When a fetched ref simply does not exist on the remote, the error message should accurately report 0 matching refs.
- Short refs with only a single path component after the refs root should be treated as valid, exact refs.
- Simple wildcard patterns with a single glob character should still return only the portion of the path before that wildcard as the prefix.
- Complex patterns with multiple wildcards or special glob syntax should continue to return no prefix, since they cannot be expressed as a simple server-side filter.

## Why This Matters

This affects the accuracy of fetch error messages and the efficiency of remote queries. With the corrected behavior, fetching a missing ref correctly tells you zero refs matched your request, and fetching a specific branch avoids downloading advertisement lists for the entire namespace.
