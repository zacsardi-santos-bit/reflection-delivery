## Description

The library currently only generates ASCII-based strings for test data — alphabetic, alphanumeric, hexadecimal, or digit characters. There is no built-in way to generate strings that include characters from other writing systems, emoji, or arbitrary Unicode code points. This severely limits the usefulness of the library for testing applications that handle international or multibyte text.

## Expected Behavior

- Developers should be able to configure the string generator to produce Unicode strings. When no specific scripts are requested, the output should contain code points spanning many different Unicode blocks from across the full Unicode space.
- Developers should also be able to restrict Unicode string generation to a specific set of writing systems or Unicode blocks. When one or more blocks are specified, the generated string should contain only code points from those blocks.
- The length configuration for Unicode strings should refer to the number of Unicode code points rather than the number of Java character units (since some code points require two Java chars).
- Generating a Unicode string of length zero should produce an empty string.
- There should be a global configuration setting that enables Unicode string generation by default for all generated strings in a test session, without having to configure it per-field.
- The global setting should be expressible as a configuration file property.
- The infrastructure for looking up code point ranges for specific Unicode blocks must be available and accurate.

## Why This Matters

Many real-world applications must handle text containing non-ASCII characters — user names in different scripts, comments with emoji, or content from international users. Without Unicode string support in test data generation, tests for such functionality must either be written manually or rely on hardcoded strings. This change makes it easy to generate realistic international text data automatically.
