Add the ability to display default values for table columns in the Gravitino CLI tool's output. Update both the plain text (CSV) and formatted table outputs to include this information, and implement a shared utility class to handle formatting logic.

*   Update `LineUtil` class:
    *   Add `public static final String EMPTY_DEFAULT_VALUE` with an empty string value.
    *   Implement `static int getDisplayWidth(String text)` to return the display width, counting ASCII as 1 and CJK/full-width characters as 2.
    *   Implement `static String getSpaces(int count)` to return a string of spaces of the specified count.
    *   Implement `static String getAutoIncrement(org.apache.gravitino.rel.Column column)` to return "true" or "false" for integer-compatible types, and an empty string for others.
    *   Implement `static String getComment(org.apache.gravitino.rel.Column column)` to return the column's comment or "N/A" if null.
    *   Implement `static String getDefaultValue(org.apache.gravitino.rel.Column column)` to format default values:
        *   Return `EMPTY_DEFAULT_VALUE` for null or `Column.DEFAULT_VALUE_NOT_SET`.
        *   Return integer literals as plain strings.
        *   Return empty string literals as `''`.
        *   Return non-empty string literals as-is.
        *   Return function expressions with no arguments as `functionName()`.
        *   Return function expressions with arguments as `functionName([args])`.

*   Update `PlainFormat` class:
    *   Implement `output(org.apache.gravitino.rel.Column[] columns, CommandContext context)` to output columns in CSV format.
    *   Use header: `name,datatype,default_value,comment,nullable,auto_increment`.
    *   Format `default_value` using `LineUtil.getDefaultValue`.
    *   Format `comment` using `LineUtil.getComment`.
    *   Format `auto_increment` using `LineUtil.getAutoIncrement`.

*   Update `TableFormat` class:
    *   Implement `output(org.apache.gravitino.rel.Column[] columns, CommandContext context)` to output columns as a formatted ASCII table.
    *   Use headers: `Name, Type, Default, AutoIncrement, Nullable, Comment`.
    *   Ensure "Default" appears between "Type" and "AutoIncrement".
    *   Format default values using `LineUtil.getDefaultValue`.
    *   Format `auto_increment` using `LineUtil.getAutoIncrement`.
    *   Format `comment` using `LineUtil.getComment`.

*   Update existing `output(org.apache.gravitino.rel.Table table, CommandContext context)` in `TableFormat`:
    *   Include a "Default" column between "Type" and "AutoIncrement".
    *   Leave the Default cell empty for columns with `DEFAULT_VALUE_NOT_SET`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.