## Description

The kube-openapi code generator currently has no way to read value-validation constraints from specially-formatted annotation comments on Go type declarations and struct fields. Developers who want constraints like numeric bounds, string length limits, pattern requirements, or cardinality restrictions to appear in the generated OpenAPI schema have to either manually edit generated output or override entire schema definitions. There is no ergonomic way to declare these constraints inline alongside the type definition and have them flow automatically into generated schemas.

## Expected Behavior

- Developers should be able to annotate Go struct types and their fields with special marker comments specifying validation constraints (e.g., minimum/maximum values, string length bounds, regex patterns, array size limits, map property counts).
- The code generator should read these marker comments and embed the corresponding constraints into the generated OpenAPI schema output, for both v2 and v3 schema formats.
- The system should validate that annotated constraints are internally consistent (e.g., minimum does not exceed maximum, patterns are valid regular expressions, multipleOf is non-zero) and report clear, specific error messages when they are not.
- The system should also validate that each constraint type is used on an appropriate Go field type (e.g., numeric constraints only on numeric fields, string constraints only on string fields) and report clear errors when there is a mismatch.
- Types that provide a complete custom schema definition should be unaffected — their marker comments should be ignored.
- Types that provide only a partial custom V3 definition should still receive annotated constraints in the V2 fallback.

## Why This Matters

Without this capability, developers must maintain validation constraints in a separate layer, leading to drift between the Go type definition and the generated schema. Having constraints expressed as source-level annotations and automatically validated at generation time makes APIs easier to maintain and reduces the risk of schema drift.
