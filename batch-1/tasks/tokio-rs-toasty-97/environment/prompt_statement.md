I'm working on a database ORM library that supports multiple SQL backends. The library has a capability system where each driver declares what SQL features it supports, and the query planner uses this information to pick the right strategy for various operations.

Right now, the capability descriptor for SQL drivers only has a flag for whether the database supports update statements inside common table expressions — but there's no way to declare whether a database supports row-level locking. This is a problem because for conditional update operations, databases that support row-level locking should use that approach, while databases without that support need to fall back to serializable transaction isolation.

I also noticed that the MySQL capability is incorrectly flagged as supporting CTE-with-update, when MySQL actually doesn't support that. MySQL does support row-level locking though.

Can you add support for expressing row-level locking capability in the SQL driver capability descriptor? The three SQL backends (MySQL, PostgreSQL, and SQLite) should each declare their correct capabilities: MySQL supports row-level locking but not CTE-with-update; PostgreSQL supports both; and SQLite supports neither.
