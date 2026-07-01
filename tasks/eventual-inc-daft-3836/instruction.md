Implement schema support for SQL table functions to read JSON and CSV files, allowing users to specify column data types directly in SQL queries. Ensure that the schema can be defined inline, similar to the Python API.

*   Update the SQL table function for reading JSON files:
    *   Accept an optional named parameter 'schema' using the ':=' syntax.
    *   Ensure the 'schema' is a dictionary literal mapping column names to type strings ('int', 'string', 'bool', 'double').
    *   Produce a DataFrame matching the Python `read_json` function with an explicit schema.

*   Update the SQL table function for reading CSV files:
    *   Accept an optional named parameter 'schema' using the ':=' syntax.
    *   Ensure the 'schema' is a dictionary literal mapping column names to type strings ('int', 'string', 'bool', 'double').
    *   Produce a DataFrame matching the Python `read_csv` function with an explicit schema.

*   Ensure the type mapping:
    *   'int' maps to int32
    *   'string' maps to string
    *   'bool' maps to bool
    *   'double' maps to float64

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.