## Description

The model server's data models module needs to support two incompatible major versions of a widely-used data validation library. Currently, there is no centralized way to detect which major version is installed, making it difficult for both tests and runtime code to handle the differences in serialization behavior between the two versions.

## Problem

When the newer major version of the library is installed, boolean fields in server health responses are serialized as native JSON booleans. When the older major version is installed, those same fields come back as stringified Python booleans. This inconsistency causes test failures because the test hardcodes one specific serialization format without accounting for the installed version.

Additionally, there is no exported flag in the data models module that indicates which major version of the validation library is active, meaning any code that needs to branch on version behavior has to re-implement the version detection logic itself.

## Expected Behavior

- The data models module should expose a boolean flag indicating whether the newer major version (version 2.x) of the validation library is installed.
- The server's health check endpoint should respond with the correct serialization format for the installed library version.
- Tests should be able to import this flag and use it to assert the correct expected response format for whichever version is installed.

## Why This Matters

Supporting both major versions of this library is important for compatibility with different deployment environments. Without a centralized version flag, every part of the codebase that needs version-specific behavior must independently detect the version, leading to fragile and inconsistent handling.
