## Description

The new version of the supply chain planning file format needs to be added to SchemaStore so that editors and validation tools can understand and validate these files. Currently, only older versions of the schema exist in the registry, which means users working with the new format get no validation or autocomplete support.

## Expected Behavior

- A valid supply plan document (with all required fields and correct types) should pass validation.
- A document missing the required schema reference property should be rejected.
- A document that includes unrecognized extra properties not defined in the schema should be rejected.
- A document with a fractional value where only whole numbers are permitted (e.g., batch/lot size) should be rejected.
- A document where a date field is provided as a raw number instead of a formatted date string should be rejected.
- A document where text identifier fields (such as material names, order names, or lot numbers) are provided as numbers instead of strings should be rejected.
- The new schema version should be registered in the catalog as the default version for this schema family.
- The schema's custom formats and keywords should be declared in the schema validation configuration so the checker does not report false errors.

## Why This Matters

Supply chain planners and developers using tools that rely on SchemaStore for validation will immediately benefit from having the new schema version available. Without it, there is no way to catch common data entry errors — like wrong types or missing required fields — before the data reaches the application.
