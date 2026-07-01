I'm working with TypeORM and I need to be able to specify a transaction isolation level when running operations inside a transaction. Right now the transaction method only accepts a callback, but I'd like to be able to pass an isolation level first so the transaction starts at that level. For example, I might want to run some operations in a serializable transaction or a read-uncommitted transaction depending on what I'm doing.

This should work both through the direct entity manager approach and through the method decorator. For the decorator, I'd like to be able to pass an options object that includes both the connection name and an isolation level, rather than just a connection name string.

Different databases support different subsets of isolation levels. For instance, SQLite only supports two modes — it should configure itself appropriately when each supported level is requested, and reject unsupported levels. Other databases that support the full range of standard isolation levels should also pass through the requested level when starting the transaction.

After the transaction completes, all entities saved within it should be committed to the database and findable via normal queries, just as they would be without specifying an isolation level.
