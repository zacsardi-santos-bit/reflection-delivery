I'm hitting a server crash in ClickHouse when trying to query a Tuple subcolumn whose name contains a dot.

*   A ClickHouse table with a Tuple column whose elements include one with a JSON type and one with a dotted name (e.g., Tuple(a JSON, `a.b` UInt32)) must be creatable and insertable without error.

*   When selecting a Tuple subcolumn by its exact dotted name (e.g., SELECT t.`a.b`), the query must return the value of that exact Tuple element rather than traversing into a same-prefix JSON element as a dynamic subcolumn path.

*   Exact name matches during subcolumn lookup must always take priority over prefix-based dynamic subcolumn matches. If a Tuple has both an element named `a` of JSON type and an element named `a.b` of UInt32 type, selecting `a.b` must return the UInt32 value (42), not the JSON subfield value (999) from element `a`.

*   The getSubcolumnData function in src/DataTypes/IDataType.cpp must be updated so that once an exact name match is found for the requested subcolumn, prefix-based dynamic subcolumn matches are no longer allowed to overwrite it.


*   Interface details: Type: Function
Name: getSubcolumnData
Location: src/DataTypes/IDataType.cpp
Signature: getSubcolumnData(std::string_view subcolumn_name, ...) -> std::unique_ptr<IDataType::SubstreamData>
Description: Resolves a named subcolumn within a data type's substream layout. Must be fixed so that exact name matches for a subcolumn always take priority over prefix-based dynamic subcolumn matches. When iterating substream paths, if the requested subcolumn name exactly matches a path name, that exact match result must take precedence — a shorter prefix path with a dynamic type (such as JSON) must not be allowed to overwrite the exact match result.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.