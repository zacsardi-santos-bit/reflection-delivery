## Description

When a user account is permanently deleted from the platform, their published blog posts remain in the system. The system stores an author's display name and biography in a separate record that gets created on demand for active users — but for deleted users, this record will never be created automatically because the underlying account no longer exists. This causes errors when blog visitors try to browse pages that include posts by deleted authors.

## Expected Behavior

We need two new batch maintenance jobs:

1. **An audit job** that scans all published blog posts, identifies author IDs whose accounts have been deleted (and who have no fallback author details record), and reports each such "orphaned" author along with a count. Authors who still have active accounts should not be flagged, even if their details record is missing, since those are handled automatically at runtime. Draft posts should be excluded entirely.

2. **A migration job** that creates placeholder author detail records (with standardized fallback display name and bio) for those deleted users. The job must be idempotent — running it multiple times must not produce duplicate records or re-process authors who were already migrated. Like the audit job, it should skip active users and draft posts.

## Why This Matters

Without these jobs, blog pages that include posts by deleted authors will continue to fail for readers. The audit job provides visibility into the scope of the problem, and the migration job provides a safe, repeatable way to fix it by backfilling the missing data with appropriate placeholder values.
