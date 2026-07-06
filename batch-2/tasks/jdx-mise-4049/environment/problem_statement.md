## Description

The embedded usage specification file used by the tool contains boolean values in an older format that is no longer compatible with the current version of the document language specification being used. The newer version of this specification requires boolean values to be written with a hash prefix rather than as bare keywords.

## Expected Behavior

- Boolean values in the embedded usage specification file should use the current format required by the newer document language version
- The file should contain the updated boolean notation so that it is compatible with current parsers and tooling that rely on the modern specification

## Why This Matters

The project has upgraded to a newer version of the KDL document language, which changed how boolean literals are represented. If the embedded specification file is not updated to match, it will be out of sync with the rest of the codebase and may cause compatibility issues with tools that expect the updated format. Keeping this file current ensures the usage specification is valid and parseable by modern tooling.
