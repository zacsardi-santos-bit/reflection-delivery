I've noticed that when I try to create or drop a user-defined function using a database-qualified name, the system doesn't validate whether the target database actually exists before attempting the operation. If the database doesn't exist, we should get a clear error indicating the database does not exist, rather than proceeding and failing with a confusing message later on.

I'd like to add a check — both when creating and when dropping user-defined functions — that verifies the specified database exists first. If it doesn't exist, the operation should be rejected immediately with an informative error. When the database does exist, the operation should proceed as normal.

There's already a pattern in the codebase for checking whether a tenant or system resource exists before taking action. I'd like to follow the same pattern and add a similar helper that checks whether a given database exists, and then integrate that check into both the function creation and function deletion flows.
