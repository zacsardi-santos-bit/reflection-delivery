Extend the lancedb Node.js library to support multiple major Apache Arrow versions. Update package configurations and ensure core utilities work seamlessly with schemas and field types from Arrow versions 13 through 17.

*   Update `nodejs/package.json`:
    *   Add devDependencies for Apache Arrow versions 13 through 17 using npm package aliasing:
        *   'apache-arrow-13' mapped to 'npm:apache-arrow@13.0.0'
        *   'apache-arrow-14' mapped to 'npm:apache-arrow@14.0.0'
        *   'apache-arrow-15' mapped to 'npm:apache-arrow@15.0.0'
        *   'apache-arrow-16' mapped to 'npm:apache-arrow@16.0.0'
        *   'apache-arrow-17' mapped to 'npm:apache-arrow@17.0.0'
    *   Update the peerDependency for `apache-arrow` to accept versions '>=13.0.0 <=17.0.0'.

*   Ensure compatibility of core utilities with multiple Arrow versions:
    *   Modify arrow table utility functions (`makeArrowTable`, `convertToTable`, `makeEmptyTable`, `fromTableToBuffer`) to accept schema or field objects from Arrow versions 13 through 17.
    *   Update the `EmbeddingFunction` class:
        *   Ensure `embeddingDataType()` returns a Float data type object from any supported Arrow version.
        *   Ensure `sourceField()` accepts DataType objects from any supported Arrow version.
    *   Ensure `LanceSchema` produces a correct schema with field types from any supported Arrow version, maintaining the input field order.
    *   Verify that the embedding registry's `register` and `getRegistry` operations work with embedding functions using types from any supported Arrow version.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.