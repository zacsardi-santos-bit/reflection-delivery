Implement automatic data type conversion for IoTDB's table model when loading data files with mismatched column types. Ensure that data is fully transferred in pipe-and-load operations without silent data loss.

Requirements:

*   Update the `TsFileTableGenerator` class located at `integration-test/src/main/java/org/apache/iotdb/it/utils/TsFileTableGenerator.java`.
    *   Ensure it implements `AutoCloseable`.
    *   Constructor: Accept a `java.io.File` argument and initialize a `TsFileWriter` for the file.
    *   Method `registerTable`: Accept a table name, a list of `IMeasurementSchema`, and a list of `Tablet.ColumnCategory`. Skip registration if the table name is already registered.
    *   Method `generateData`: Accept a table name, a row count (int), a time gap (long), and an `isAligned` flag (boolean). Generate random data rows of appropriate types for each registered column and write them to the TsFile using `writeTable` when `isAligned` is false.
    *   Method `getTotalNumber`: Return a long representing the total number of data points generated across all registered tables.
    *   Method `close`: Close the underlying `TsFileWriter`.

*   Ensure the following behaviors for the table model:
    *   When a TsFile with mismatched column types is loaded into a database table with compatible types, the load must succeed and perform automatic data type conversion.
    *   After loading, executing `select count(*) from <tableName>` must return the exact number of rows written into the TsFile.
    *   The load operation must work with the `TABLE_SQL_DIALECT`, targeting a database named 'root' and a table named 'test'.

*   In pipe-and-load scenarios:
    *   Ensure querying `select * from t1` includes all rows from both source tables with null values for non-applicable columns.
    *   The column header must reflect the union of all tag and field columns from both sources.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.