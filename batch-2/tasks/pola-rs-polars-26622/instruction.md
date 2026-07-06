I'm working with Polars' SQL interface and running into an issue with typed literals inside array constructors.

*   When a SQL ARRAY[] literal contains temporal typed literals (DATE 'YYYY-MM-DD', TIME 'HH:MM:SS', TIMESTAMP(n) 'YYYY-MM-DD HH:MM:SS'), the resulting list column must have the correct Polars type (pl.List(pl.Date), pl.List(pl.Time), pl.List(pl.Datetime('ms', time_zone=None)) for TIMESTAMP(3)) and contain accurate Python date/time/datetime values.

*   When a SQL ARRAY[] literal contains elements with Postgres-style cast syntax ('...'::date, '...'::time, '...'::bigint, '...'::double), the resulting list column must carry the corresponding Polars type (pl.List(pl.Date), pl.List(pl.Time), pl.List(pl.Int64), pl.List(pl.Float64)) with correct values.

*   When a SQL ARRAY[] literal contains elements using explicit CAST('...' AS TYPE) syntax, the resulting list column must carry the correct Polars type (e.g., pl.List(pl.Date)) with accurate values.

*   When a SQL ARRAY[] literal contains nested arrays with typed/cast elements (e.g., ARRAY[['42'::int16], ['-7'::int16]]), the resulting column must have type pl.List(pl.List(pl.Int16)) with correct nested values.

*   When a SQL query uses nested bracket-syntax array literals with typed elements (e.g., [[DATE '...', DATE '...'], [DATE '...']]), the resulting column must have the appropriate nested list type with correct values.

*   When a SQL ARRAY[] literal contains elements of incompatible types (e.g., mixing DATE and TIME literals), pl.sql() must raise SQLInterfaceError (from polars.exceptions) with an error message matching the pattern 'expected consistent dtypes'.

*   The SQL ARRAY[...] keyword syntax (with the ARRAY keyword prefix) must be the supported form for constructing array literals; bare bracket-only syntax [10,20,30] without the ARRAY keyword is not required for the existing array literals test.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.