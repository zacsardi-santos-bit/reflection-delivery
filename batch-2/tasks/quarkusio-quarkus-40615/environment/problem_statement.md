## Description

A utility class used for managing system properties in a resettable manner was placed inside the core runtime module. This module is included as a dependency of all Quarkus applications and ends up in production builds. The utility in question is only useful during testing or development — it is not needed at runtime by deployed applications.

## Expected Behavior

- The core runtime module should not expose or include utilities that are only relevant during testing or development.
- Any class that handles resettable system property management for test purposes should live outside the core runtime module so it is not on the classpath of production builds.

## Why This Matters

Having test/development utilities in the core runtime module means every application that depends on Quarkus carries unnecessary code. This violates module separation principles and can lead to confusion about what is part of the production runtime. Removing this utility from the runtime module ensures a clean, lean runtime artifact and enforces correct module boundaries.
