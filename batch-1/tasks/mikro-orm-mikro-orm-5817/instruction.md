Ensure the entity generator accurately reflects all relevant column attributes when creating scalar properties for foreign key columns in TypeScript entity files. Implement the following requirements to propagate metadata from the database schema to the generated code.

*   Include `unsigned: true` in the options of a scalar property if the foreign key column is unsigned.
    *   Apply this to both decorator-style and EntitySchema-style outputs.
*   Include the `length` attribute in a scalar property if the foreign key column has a fixed character length.
    *   Set the `length` attribute to match the column's character length (e.g., CHAR(2), VARCHAR(36)).
*   Include `autoincrement: true` in the options of a scalar property if the foreign key column has AUTO_INCREMENT set.
*   Include a `columnType` attribute in a scalar property if the foreign key column uses a non-standard database-specific type.
    *   Set the `columnType` attribute to the native type string (e.g., `columnType: 'year'`).
*   Apply these metadata propagation rules consistently across all entity generator output modes:
    *   Decorator-style entities
    *   EntitySchema-style entities
    *   With or without bidirectional relations
    *   With or without identified references

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.