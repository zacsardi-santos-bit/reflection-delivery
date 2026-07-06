## Description

The curated hub code currently uses plain Python dictionaries with non-standard, lowercase field names to represent hub content summary data returned from AWS list APIs. This is inconsistent with the actual API response structure, which uses proper AWS-style field names. As a result, downstream code that tries to work with these summaries is brittle and error-prone — for example, when comparing versions or filtering models.

There is also no way to inspect models stored in a curated hub to determine whether particular versions are deprecated or have known security vulnerabilities. Operators need the ability to scan a hub's models and tag each one with version-level information about which versions are deprecated, which have inference vulnerabilities, and which have training vulnerabilities — so that users can be informed and avoid problematic versions.

## Expected Behavior

- A structured data object should represent hub content summaries, with fields that map cleanly to the AWS API response field names (using proper naming conventions).
- Utility functions should be available to convert a raw AWS list API response — both a single entry and a full list response — into these structured data objects.
- An enum should define the possible unsupported-model flag types (deprecated, inference vulnerable, training vulnerable).
- A utility function should determine which unsupported flags apply to a specific model version by checking the public JumpStart model catalog.
- A utility function should scan all versions of a specific hub content item and return tag-style records indicating which versions are affected by each type of flag.
- The hub's internal methods for listing and syncing models must work with the new structured data objects rather than plain dicts.

## Why This Matters

Using a proper data structure for hub content summaries makes the code more robust and self-documenting, and aligns with the actual AWS API response format. The ability to scan and tag deprecated or vulnerable model versions gives operators the tooling needed to keep their curated hubs healthy and inform users about models they should avoid.
