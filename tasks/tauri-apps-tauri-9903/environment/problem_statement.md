## Description

The documentation string for the capability concept in Tauri's access control layer starts with a lowercase letter. This is inconsistent with standard documentation conventions in the project and causes automated checks to fail. When this documentation is converted into a JSON schema for use by editors and other tooling, the schema description inherits the lowercase starting character, resulting in an inconsistently formatted artifact.

## Expected Behavior

- The description for the capability type should start with an uppercase letter.
- The JSON schema generated from the capability type should have a properly capitalized description that begins with an uppercase letter.

## Why This Matters

Auto-generated schema files are consumed by editors and tooling that rely on well-formed documentation strings. A lowercase first letter in the description is inconsistent with the rest of the project's documentation conventions, causes CI checks to fail, and results in improperly formatted schema artifacts being shipped.
