# Database Collation: Enforce Case-Sensitive Collation and Support Emoji Branch Names

## Description

Gitea has two related issues around character encoding and case sensitivity.

**Issue 1: MySQL/MSSQL case-insensitive collation causes silent data conflicts**

When Gitea is running on MySQL or MSSQL, these databases default to case-insensitive collations. This means that branch names, usernames, or other data that differ only in letter casing (e.g., "Branch" vs "branch") are treated as identical by the database, causing unexpected conflicts. Gitea should enforce case-sensitive collation at the database and table level so that uppercase and lowercase values are treated as distinct.

There should also be a way to inspect the current state of database collations — checking whether the database is configured correctly, what collation is expected, what collations are available, and which tables/columns (if any) have inconsistent collations.

Additionally, there should be a mechanism to convert existing tables to use the correct case-sensitive collation (or a user-specified collation), and this should be usable as part of a database maintenance command.

**Issue 2: Emoji and extended Unicode characters are rejected in branch names**

Users should be able to create branches with names that contain emoji or other extended Unicode characters. Currently, attempting to create such a branch via the API fails, even though git itself can handle these names.

## Expected Behavior

- Newly created database tables should use a case-sensitive collation by default, meaning two values that differ only in case can coexist under a UNIQUE constraint.
- A collation inspection facility should report the current database collation, the expected collation, available collations, and any columns with inconsistent collations.
- The collation inspection should provide helpers to determine if a specific collation is case-sensitive and to compare collations for equality (with appropriate normalization for each database engine).
- A table conversion tool should be able to bring all tables into alignment with either the configured collation or a sensible case-sensitive default.
- Branch names containing emoji characters should be accepted by the API and result in successful branch creation.

## Why This Matters

Without case-sensitive collation, data integrity is at risk: entities that should be distinct are treated as duplicates at the database level. This is especially problematic for multi-user environments where branch names and usernames must be uniquely identifiable. Supporting emoji in branch names allows users to use modern naming conventions that are valid in git.
