Implement support for modifying properties on Iceberg views in Spark, allowing users to set and remove metadata using SQL commands. Ensure reserved properties are protected from modification and enforce consistent naming conventions for internal properties.

*   Update the `SparkView` class:
    *   Ensure the internal constant `QUERY_COLUMN_NAMES` is set to "spark.query-column-names".
    *   Expose a public `RESERVED_PROPERTIES` set containing at least: "provider", "location", "format-version", and "spark.query-column-names".

*   Implement the `alterView` method in the `SparkCatalog` class:
    *   Handle `ViewChange.SetProperty` and `ViewChange.RemoveProperty` changes.
    *   For `SetProperty` changes:
        *   Check if the property is in `RESERVED_PROPERTIES`.
        *   Throw `UnsupportedOperationException` with the message "Cannot set reserved property: '<property>'" if the property is reserved.
    *   For `RemoveProperty` changes:
        *   Check if the property is in `RESERVED_PROPERTIES`.
        *   Throw `UnsupportedOperationException` with the message "Cannot unset reserved property: '<property>'" if the property is reserved.
        *   If the property does not exist and the "IF EXISTS" clause is not used, throw `AnalysisException` with the message "Cannot remove property that is not set: '<property>'".
        *   If the "IF EXISTS" clause is used and the property is not reserved, do not throw an exception.
    *   Apply non-reserved property changes using the Iceberg view's `UpdateViewProperties` API and commit the changes.

*   Ensure SQL command support:
    *   Implement `ALTER VIEW <name> SET TBLPROPERTIES` to persist specified properties on the view.
    *   Implement `ALTER VIEW <name> UNSET TBLPROPERTIES` to remove specified properties from the view.

*   Reserved property handling:
    *   Setting 'format-version' or 'spark.query-column-names' must throw `UnsupportedOperationException` with appropriate error messages.
    *   Unsetting 'format-version' must throw `UnsupportedOperationException`.
    *   Unsetting 'spark.query-column-names' without "IF EXISTS" must throw `AnalysisException`. With "IF EXISTS", it must throw `UnsupportedOperationException`.

*   Ensure the internal property key for query column names is renamed to "spark.query-column-names" and treated as a reserved property.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.