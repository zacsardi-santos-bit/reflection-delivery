I'm working with a table that has a struct column stored with dictionary encoding — the kind of layout you get from Parquet or Arrow data where repeated struct values are deduplicated and stored as dictionary entries.

*   The field extraction operation (both bracket notation and explicit function call) must support columns of dictionary-encoded struct type — that is, a dictionary whose value type is a struct.

*   When extracting a named field from a dictionary-encoded struct column, the result type must preserve the dictionary encoding: the output type is a dictionary with the same key type and the field's value type as the value type (e.g., extracting a Utf8 field from Dictionary(UInt32, Struct(...)) yields Dictionary(UInt32, Utf8)).

*   When the dictionary key for a row is NULL (absent), field extraction on that row must return NULL regardless of which field is accessed.

*   Filtering, aggregating (GROUP BY), and ordering (ORDER BY) on fields extracted from dictionary-encoded struct columns must produce correct results consistent with the same operations on equivalent non-dictionary-encoded struct columns.

*   Attempting to extract a field name that does not exist in the struct definition must produce an error.

*   A test table named 'dict_struct_table' must be registered with two columns: 'dict_struct' of type Dictionary(UInt32, Struct{name: Utf8, id: Int32}) with 5 non-null rows (Alice/1, Bob/2, Carol/3, Alice/1, Bob/2), and 'plain_struct' of type Struct{name: Utf8, id: Int32} with the same data.

*   A test table named 'dict_struct_nullable' must be registered with one column 'ds' of type Dictionary(UInt32, Struct{name: Utf8, id: Int32}) with 4 rows where keys at positions 1 and 3 are NULL (logical rows: {X,10}, NULL, {Y,20}, NULL).

*   The function register_dictionary_struct_table must be called for the 'dictionary_struct.slt' test file in the test context dispatcher in datafusion/sqllogictest/src/test_context.rs.


*   Interface details: Type: Function
Name: register_dictionary_struct_table
Location: datafusion/sqllogictest/src/test_context.rs
Signature: register_dictionary_struct_table(ctx: &SessionContext)
Description: Registers two test tables for the dictionary_struct.slt test file. First, registers 'dict_struct_table' with two columns: 'dict_struct' of type Dictionary(UInt32, Struct{name: Utf8, id: Int32}) with 5 non-null rows (Alice/1, Bob/2, Carol/3, Alice/1, Bob/2 via keys [0,1,2,0,1] into a 3-entry values array), and 'plain_struct' of type Struct{name: Utf8, id: Int32} with the same data. Second, registers 'dict_struct_nullable' with a single column 'ds' of type Dictionary(UInt32, Struct{name: Utf8, id: Int32}) with 4 rows where keys at positions 1 and 3 are NULL (logical rows: {X,10}, NULL, {Y,20}, NULL). The function must also be registered in the test context dispatcher match arm for "dictionary_struct.slt".

Type: Test File
Name: dictionary_struct.slt
Location: datafusion/sqllogictest/test_files/dictionary_struct.slt
Description: SQL logic test file that tests field extraction on dictionary-encoded struct columns. Tests bracket notation and get_field() function for extracting fields, verifies output types via arrow_typeof(), tests NULL propagation, filtering, aggregation, ordering, and that accessing a non-existent field causes an error.

Type: Function (existing, must be extended)
Name: extract_single_field (internal to GetFieldFunc)
Location: datafusion/functions/src/core/getfield.rs
Signature: extract_single_field(base: ColumnarValue, name: ScalarValue) -> Result<ColumnarValue>
Description: The existing internal function implementing get_field must be extended to handle DataType::Dictionary(key_type, value_type) when value_type is a Struct. When encountered, it should extract the named field from the dictionary's values array and return a new dictionary with the same keys but values being the extracted field column. This preserves dictionary encoding in the output.

Type: Method (existing, must be extended)
Name: return_type (on GetFieldFunc)
Location: datafusion/functions/src/core/getfield.rs
Description: The return_type inference logic for GetFieldFunc must be extended to handle DataType::Dictionary(key_type, value_type) when value_type is a Struct. The inferred output type for extracting field F (of type T) from Dictionary(K, Struct{..., F: T, ...}) must be DataType::Dictionary(K, T), not T alone.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.