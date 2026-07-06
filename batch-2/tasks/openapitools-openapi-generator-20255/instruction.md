Implement a PostgreSQL schema generator in the `PostgresqlSchemaCodegen` class to convert OpenAPI model definitions into PostgreSQL-compatible SQL schema files. Ensure intelligent data type mapping, proper identifier handling, and configurable options for database generation.

*   Implement the `PostgresqlSchemaCodegen` class:
    *   Expose static string constants: `DEFAULT_DATABASE_NAME`, `JSON_DATA_TYPE`, `IDENTIFIER_NAMING_CONVENTION`, `NAMED_PARAMETERS_ENABLED`, `ID_AUTOINC_ENABLED`.
    *   Set the generator language name to 'postgresql-schema'.

*   Implement data type mapping methods:
    *   `getPostgresqlMatchedIntegerDataType(Long minimum, Long maximum, Boolean unsigned) -> String`:
        *   Return 'INTEGER' when all arguments are null.
        *   Return 'SMALLINT' for ranges within [-32768, 32767].
        *   Return 'INTEGER' for ranges within Integer.MIN_VALUE to Integer.MAX_VALUE.
        *   Return 'BIGINT' for ranges exceeding INTEGER capacity.
    *   `getPostgresqlMatchedStringDataType(Integer minLength, Integer maxLength) -> String`:
        *   Return 'VARCHAR' for maxLength <= 255.
        *   Return 'TEXT' when maxLength is null or >= 16777215.

*   Implement identifier handling:
    *   `toPostgresqlIdentifier(String name, String prefix, String suffix) -> String`:
        *   Return the trimmed identifier as-is for valid names.
        *   Prepend the prefix if the name starts with a digit.
        *   Throw `RuntimeException` if the trimmed name is blank.
    *   `escapePostgresqlUnquotedIdentifier(String identifier) -> String`:
        *   Retain alphanumeric characters, dollar signs, underscores, and non-ASCII characters.
        *   Remove ASCII special characters and supplementary Unicode characters.
    *   `escapePostgresqlQuotedIdentifier(String identifier) -> String`:
        *   Remove supplementary Unicode characters and trim trailing whitespace.
        *   Retain most other characters.

*   Implement configuration options:
    *   `setDefaultDatabaseName(String name) -> void` and `getDefaultDatabaseName() -> String`:
        *   Initial value is an empty string.
        *   Accept names starting with a letter.
        *   Silently reject names starting with a digit.
    *   `setJsonDataType(String type) -> void` and `getJsonDataType() -> String`:
        *   Initial value is 'json'.
        *   Accept 'json', 'jsonb', and 'off'.
    *   `setIdentifierNamingConvention(String convention) -> void` and `getIdentifierNamingConvention() -> String`:
        *   Initial value is 'snake_case'.
        *   Accept 'snake_case' and 'original'.
        *   Silently ignore invalid values.
    *   `setNamedParametersEnabled(Boolean enabled) -> void` and `getNamedParametersEnabled() -> Boolean`:
        *   Initial value is false.
    *   `setIdAutoIncEnabled(Boolean enabled) -> void` and `getIdAutoIncEnabled() -> Boolean`:
        *   Initial value is false.

*   Implement utility methods:
    *   `toCodegenPostgresqlDataTypeArgument(Object argument) -> HashMap<String, Object>`:
        *   Return a map describing the argument type.
    *   `toCodegenPostgresqlDataTypeDefault(String defaultValue, String dataType) -> HashMap<String, Object>`:
        *   Classify a default value for a given PostgreSQL data type.
    *   `isPostgresqlDataType(String typeName) -> boolean`:
        *   Return true for recognized PostgreSQL data types.
    *   `reservedWords() -> Set<String>`:
        *   Return a set of PostgreSQL reserved words.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.