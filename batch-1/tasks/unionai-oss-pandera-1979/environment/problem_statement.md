## Description

Pandera's Polars backend currently lacks integration with pydantic — the widely-used Python library for data validation via type annotations. This means developers cannot use pandera-validated Polars DataFrames or schema objects as fields in pydantic models, which is a common pattern when building APIs, configuration schemas, or data pipelines that combine pandera schema validation with pydantic's structured Python models.

Additionally, the typed Polars DataFrame class does not support reading from or writing to common data interchange formats (CSV, JSON, Parquet, Feather), even though the equivalent pandas-backed class does. This inconsistency makes it harder to build format-aware pipelines with the Polars backend.

## Expected Behavior

- A typed Polars DataFrame annotated with a schema should be usable as a pydantic model field. Valid dataframes pass validation; invalid ones raise a validation error.
- Schema model subclasses and schema instances should be accepted as pydantic field values with correct type-checking behavior, including inheritance (a child model is accepted where a parent is declared, an unrelated model is not).
- Schemas with optional columns should work correctly: missing optional columns are fine, wrong types for optional columns are rejected, and missing required columns are rejected.
- Inputs like dicts and pandas DataFrames passed to a typed DataFrame field should be automatically converted to Polars DataFrames.
- The typed Polars DataFrame class should support reading from CSV, JSON, Parquet, Feather, and dict formats, and writing to those same formats, with clear error messages on failure.
- Unsupported formats (such as pickle) should raise a descriptive error rather than producing cryptic failures.

## Why This Matters

Users building APIs or data pipelines with the Polars backend currently cannot take advantage of pandera's type-annotated schema validation within pydantic models, forcing them to manually wire up validation. Consistent format-conversion support also makes the Polars backend a first-class citizen alongside the pandas backend.
