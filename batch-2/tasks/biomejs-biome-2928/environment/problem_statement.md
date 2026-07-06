## Description

The GraphQL parser in Biome does not support object type extension statements. GraphQL schemas commonly use extensions to augment existing types — adding new fields, attaching directives, or declaring that a type implements additional interfaces — especially in federated or composed schemas. Without this support, any schema that uses this pattern cannot be parsed, which blocks formatting, linting, and other tooling for a large class of real-world GraphQL documents.

## Expected Behavior

- All valid forms of object type extension must be parsed without errors:
  - Extension with only a fields definition block
  - Extension with only directives
  - Extension with only an implements clause
  - Extension with both implements and directives
  - Extension with directives and a fields block
  - Extension with all three (implements, directives, and fields)

- When an extension statement is written but includes none of the three required additions, the parser should report a clear error explaining that at least one directive, implements clause, or fields definition is required.

- When a fields block appears but its opening delimiter is missing, the parser should report a targeted error indicating the missing delimiter, along with a suggestion to remove the unexpected token that was found instead.

- After encountering an invalid extension statement, the parser should recover and continue parsing the remainder of the document correctly.

## Why This Matters

Without support for this construct, any GraphQL schema that builds on existing types via extensions is completely unprocessable by Biome's tooling. Adding proper support — including helpful diagnostics for common mistakes — makes the parser work correctly with the full range of GraphQL schema patterns in use today.
