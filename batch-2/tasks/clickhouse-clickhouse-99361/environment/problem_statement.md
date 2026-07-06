# Text indexes on map values are not used for IN-operator queries

## Description

When a table is created with a text search index defined on the collection of all values in a map column, that index is not applied when querying with a map subscript access and the IN operator. For example, filtering rows where a specific map entry's value is one of several candidates does not benefit from granule skipping, even though a suitable index exists.

This means that queries filtering map data by value membership always scan every granule, providing no performance benefit from the index.

## Expected Behavior

- When filtering a map column using subscript access and IN against a non-empty list of values, the text index built on map values should be used to skip irrelevant granules.
- When both a map-keys index and a map-values index exist, both should cooperate: the keys index first narrows to granules where the queried key is present, then the values index further narrows to granules where one of the target values appears.
- When map subscript access appears inside a tuple IN expression alongside other indexed columns, both the map-values index and the other column's index should be applied.

## Correctness Edge Case

When the IN condition includes the empty string, the index must **not** be used to skip granules. Map subscript access on a missing key returns the empty string as a default, so any granule that does not contain the queried key would appear to have an empty-string value. Skipping such granules would incorrectly exclude matching rows from the result.

## Why This Matters

Map columns are commonly used to store variable-length attribute data. Without index support for IN filtering on map values, analytical workloads that filter by specific map entry values must perform full scans, defeating the purpose of defining text indexes on the map column.
