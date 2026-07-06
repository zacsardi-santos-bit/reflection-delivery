## Description

The Kotlin Misk server code generator is missing several configuration options that developers need to customize their generated server code. Currently, there is no way to specify a prefix that gets applied to all generated API action paths, no option to control whether stub implementation classes are generated alongside interfaces, and no option to toggle whether generated model classes include annotations for a popular serialization library. Additionally, the generator's declared feature set incorrectly omits JSON as a supported wire format, even though the generated server code handles JSON requests and responses.

## Expected Behavior

- The generator should accept a configurable path prefix that gets prepended to all API action route paths.
- The generator should have an option to control whether stub implementation classes are generated (defaulting to disabled).
- The generator should have an option to control whether model classes are annotated for JSON serialization (defaulting to enabled).
- The generator's declared supported wire formats should include JSON in addition to the already-declared binary protocol buffer format.

## Why This Matters

Without these options, developers using the Kotlin Misk generator cannot customize basic structural aspects of their generated code and are forced to manually edit generated files. The incorrect wire format declaration may also cause the generator to be excluded from tool searches that filter by supported formats.
