I'm hitting a wall with the AWS SDK V1-to-V2 migration tool. After it runs, the output still doesn't compile in two common enum cases and I'd like to fix both.

First one is enum constants. The old SDK uses mixed-case names for enum constants, the new SDK uses all-uppercase-with-underscores. The tool rewrites the type imports fine so they point at the new SDK types, but it never renames the constant references themselves, so you get code that imports the new type yet still refers to constants with the old naming, and those just don't exist in V2. I want it to automatically convert those mixed-case constant references to the uppercase-underscore convention when migrating.

Second one is getter methods. Old SDK has getters on model objects that return enum types directly. New SDK replaced those with string-returning variants that follow a predictable pattern, single values get a string suffix and list/collection values get a plural string suffix. The tool doesn't touch these calls at all right now so they point at methods that no longer exist. I want those enum-returning getter calls rewritten to their string-returning equivalents.

Both transformations need to cover at least the SQS, SNS, and DynamoDB service models so the migrated code actually compiles.

Oh and one more thing while you're in here, the migration integration test only applies version substitution to the "after" project's build file, not the "before" one. Fix that so both build files get the version applied consistently before building.

Without this stuff the migrated code won't compile even after the tool runs, which means hand-fixing every enum reference and getter call across a big codebase, so getting these automated is the whole point.
