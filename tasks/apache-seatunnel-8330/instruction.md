Implement the `DebeziumJsonDeserializationSchema` class to correctly deserialize Debezium JSON-formatted change data capture events from MySQL, SQL Server, Oracle, and PostgreSQL into SeaTunnel rows. Ensure that all connector-specific type encodings are accurately converted to the appropriate SeaTunnel types.

*   Implement the `deserialize(byte[] message, Collector<SeaTunnelRow> out)` method to handle the following conversions:
    *   For MySQL:
        *   Convert tinyint(1) integer values (0/1) to Boolean.
        *   Convert tinyint values to Byte, tinyint unsigned/smallint to Short, smallint unsigned/mediumint/int to Integer, and bigint/bigint unsigned to Long.
        *   Convert float to Float, double to Double, and decimal/numeric JSON numbers to BigDecimal.
        *   Convert char/varchar/text/json/enum to String.
        *   Convert bit(1) JSON booleans to Boolean and bit(64) base64 strings to Boolean.
        *   Convert binary/varbinary/blob base64 strings to byte[].
        *   Convert date integer days since epoch to LocalDate, time long microseconds to LocalTime, year integers to Integer, datetime long milliseconds to LocalDateTime, and timestamp ISO strings to LocalDateTime.
    *   For SQL Server:
        *   Convert bit JSON booleans to Boolean, int to Integer, bigint to Long, real to Float, float to Double, and decimal JSON numbers to BigDecimal.
        *   Convert char/nchar/varchar/nvarchar to String and binary base64 strings to byte[].
        *   Convert date integer days since epoch to LocalDate, time long nanoseconds to LocalTime, time(3) milliseconds to LocalTime, datetime long milliseconds to LocalDateTime, datetime2 long nanoseconds to LocalDateTime, and datetimeoffset ISO strings to LocalDateTime.
    *   For Oracle:
        *   Convert INTEGER JSON integers to Integer and NUMBER/FLOAT JSON objects to BigDecimal.
        *   Convert CHAR/LONG/CLOB to String.
        *   Convert DATE long milliseconds to LocalDateTime, TIMESTAMP long microseconds to LocalDateTime, and TIMESTAMP WITH TIME ZONE ISO strings to LocalDateTime.
    *   For PostgreSQL:
        *   Convert boolean to Boolean, smallint/int to Integer, varchar/char/text to String.
        *   Convert date integer days since epoch to LocalDate, time long microseconds to LocalTime, time(3) milliseconds to LocalTime, and time with timezone strings to LocalTime.
        *   Convert timestamp long microseconds to LocalDateTime, timestamp(3) milliseconds to LocalDateTime, and timestamp with timezone ISO strings to LocalDateTime.

*   Ensure the constructor signature `DebeziumJsonDeserializationSchema(CatalogTable catalogTable, boolean ignoreParseErrors, boolean debeziumEnable)` is used in tests.
*   Access field values from `SeaTunnelRow` using `getField(int index)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.