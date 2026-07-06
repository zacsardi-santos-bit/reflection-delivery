## Description

Iceberg views in the Spark integration currently have no support for modifying properties after they've been created. Users cannot add metadata labels, update comments, or manage custom key-value annotations on views using standard SQL statements. This is a gap compared to table property management, which is well-supported.

Additionally, the internal property key used to track which columns appear in a view's defining query is not following a consistent naming convention, making it harder to distinguish Spark-specific internal properties from user-defined ones.

## Expected Behavior

- Users should be able to set one or more properties on an existing view using standard SQL, and those properties should be persisted and retrievable from the view catalog.
- Users should be able to remove previously set properties from a view using standard SQL, optionally with an "if exists" qualifier to suppress errors for missing properties.
- Attempting to set or unset internal reserved properties (such as format version or the internal query column names key) must fail with a clear error indicating the property is reserved.
- Attempting to remove a property that was never set (without the "if exists" qualifier) must fail with a clear error identifying the specific property.
- When the "if exists" qualifier is used but the targeted property is reserved, the operation must still fail with an appropriate reserved-property error.
- The internal property key used to store query column names should be renamed to follow the Spark-specific naming convention (prefixed appropriately to indicate its internal, Spark-scoped nature).

## Why This Matters

Supporting view property management makes Iceberg views a first-class object alongside tables in Spark SQL workflows. Users can annotate views with metadata like comments, ownership tags, or other custom labels, and can manage these properties over time. Proper protection of internal reserved properties ensures that users cannot accidentally corrupt critical internal view metadata.
