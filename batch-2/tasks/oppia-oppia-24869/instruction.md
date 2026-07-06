I'm working on a blog platform where user accounts can be permanently deleted.

*   The module must define two module-level string constants: DELETED_USER_FALLBACK_AUTHOR_NAME (value: 'Deleted User') and DELETED_USER_FALLBACK_AUTHOR_BIO (value: '' — an empty string). These constants are referenced externally and must be accessible by name from the module.

*   AuditBlogAuthorDetailsForDeletedUsersJob must process only published BlogPostSummaryModel entries (those where published_on is not None). Draft posts (published_on is None) must be excluded entirely.

*   AuditBlogAuthorDetailsForDeletedUsersJob must identify 'orphaned' author IDs: author IDs that appear in at least one published blog post summary, have no corresponding BlogAuthorDetailsModel, AND have no UserSettingsModel (i.e., the user has been deleted). Authors who still have a UserSettingsModel must NOT be flagged as orphaned even if they lack a BlogAuthorDetailsModel.

*   AuditBlogAuthorDetailsForDeletedUsersJob must emit a stdout result 'ORPHANED AUTHOR ID: {author_id}' for each orphaned author ID found, and 'ORPHANED AUTHOR IDS COUNT SUCCESS: {n}' reporting the total number of orphaned author IDs. Multiple blog posts by the same orphaned author must count as one orphaned author ID (deduplicated).

*   AuditBlogAuthorDetailsForDeletedUsersJob must always emit 'TOTAL BLOG POST AUTHOR IDS COUNT SUCCESS: {n}' reporting the total number of unique author IDs across all published blog post summaries, regardless of whether any are orphaned. When storage is empty or all posts are drafts, the job must produce no output at all.

*   MigrateBlogAuthorDetailsForDeletedUsersJob must process only published BlogPostSummaryModel entries (published_on is not None). Draft posts must be excluded from migration.

*   MigrateBlogAuthorDetailsForDeletedUsersJob must create a new BlogAuthorDetailsModel for each orphaned author ID (published blog post, no UserSettingsModel, no existing BlogAuthorDetailsModel). The created model must have displayed_author_name equal to DELETED_USER_FALLBACK_AUTHOR_NAME and author_bio equal to DELETED_USER_FALLBACK_AUTHOR_BIO.

*   MigrateBlogAuthorDetailsForDeletedUsersJob must emit a stdout result 'MIGRATED AUTHOR ID: {author_id}' for each newly created BlogAuthorDetailsModel, and 'MIGRATED AUTHOR DETAILS COUNT SUCCESS: {n}' reporting the total number of newly created models. When no migration is needed (empty storage, all posts have matching details, or all deleted users already have details), the job must produce no output.

*   MigrateBlogAuthorDetailsForDeletedUsersJob must skip author IDs that already have a BlogAuthorDetailsModel (idempotent — running the job multiple times must not create duplicate records or produce output for already-migrated authors).

*   MigrateBlogAuthorDetailsForDeletedUsersJob must skip author IDs that still have a UserSettingsModel (active users). Only truly deleted users — those with no UserSettingsModel — must be migrated.


*   Interface details: Type: Module
Name: blog_author_details_migration_jobs
Location: core/jobs/batch_jobs/blog_author_details_migration_jobs.py
Description: New module containing audit and migration batch jobs for blog author details records belonging to deleted users.

Type: Constant
Name: DELETED_USER_FALLBACK_AUTHOR_NAME
Location: core/jobs/batch_jobs/blog_author_details_migration_jobs.py
Description: Module-level string constant used as the displayed_author_name when creating a BlogAuthorDetailsModel for a deleted user. Value: 'Deleted User'.

Type: Constant
Name: DELETED_USER_FALLBACK_AUTHOR_BIO
Location: core/jobs/batch_jobs/blog_author_details_migration_jobs.py
Description: Module-level string constant used as the author_bio when creating a BlogAuthorDetailsModel for a deleted user. Value: '' (empty string).

Type: Class
Name: AuditBlogAuthorDetailsForDeletedUsersJob
Location: core/jobs/batch_jobs/blog_author_details_migration_jobs.py
Description: Read-only audit job that identifies published blog posts whose authors have been deleted (no UserSettingsModel) and have no BlogAuthorDetailsModel. Produces stdout messages: 'ORPHANED AUTHOR ID: {author_id}' for each orphaned author_id, 'ORPHANED AUTHOR IDS COUNT SUCCESS: {n}' for the total count of orphaned authors, and 'TOTAL BLOG POST AUTHOR IDS COUNT SUCCESS: {n}' for the total count of unique published blog post author IDs. Produces no output when storage is empty or no orphaned authors exist (except for the total count when published posts are present).
Signature: run(self) -> beam.PCollection[job_run_result.JobRunResult]

Type: Class
Name: MigrateBlogAuthorDetailsForDeletedUsersJob
Location: core/jobs/batch_jobs/blog_author_details_migration_jobs.py
Description: Migration job that creates BlogAuthorDetailsModel entries for deleted users who have published blog posts. For each such author_id, creates a BlogAuthorDetailsModel with displayed_author_name=DELETED_USER_FALLBACK_AUTHOR_NAME and author_bio=DELETED_USER_FALLBACK_AUTHOR_BIO. Produces stdout messages: 'MIGRATED AUTHOR ID: {author_id}' for each migrated author, and 'MIGRATED AUTHOR DETAILS COUNT SUCCESS: {n}' for the total count. Produces no output when no migration is needed. Skips active users (those with a UserSettingsModel), skips already-migrated authors (those with an existing BlogAuthorDetailsModel), and skips draft posts (published_on is None).
Signature: run(self) -> beam.PCollection[job_run_result.JobRunResult]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.