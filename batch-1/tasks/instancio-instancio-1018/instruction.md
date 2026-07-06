Implement support for generating Unicode strings in the Java test data generation library. Ensure the ability to generate strings with code points from various Unicode blocks, and allow configuration for specific blocks or a global default setting.

*   Implement the `unicode` method in `StringGeneratorSpec` and `StringSpec` interfaces:
    *   Method signature: `unicode(Character.UnicodeBlock... blocks) -> StringGeneratorSpec` and `unicode(Character.UnicodeBlock... blocks) -> StringSpec`.
    *   Generate strings with code points from multiple Unicode blocks when called with no arguments.
    *   Restrict code points to specified blocks when arguments are provided.
    *   Ensure the number of code points matches the specified length.

*   Implement the `StringType` enum in `org.instancio.settings`:
    *   Include values: ALPHABETIC, ALPHANUMERIC, DIGITS, HEX, and UNICODE.

*   Define the `STRING_TYPE` field in `org.instancio.settings.Keys`:
    *   Type: `SettingKey<StringType>`.
    *   Property name: "string.type".
    *   Default value: `StringType.ALPHABETIC`.

*   Implement global Unicode string generation:
    *   When `Keys.STRING_TYPE` is set to `StringType.UNICODE`, generate Unicode strings by default.
    *   Ensure code points span multiple Unicode blocks and match the configured length.

*   Develop the `UnicodeBlocks` utility class in `org.instancio.internal.util`:
    *   Provide a static `getInstance()` method.
    *   Implement `getRange(UnicodeBlock block) -> BlockRange` to return correct code point ranges.
    *   Ensure `BlockRange` includes `min()` and `max()` methods with correct values for CYRILLIC, EMOTICONS, and TAGS blocks.

*   Register `StringType` in `SettingsSupport` for enum parsing:
    *   Include `StringType.class` in the internal function map for parsing from string values.

*   Ensure the configuration property 'string.type' is recognized in `instancio.properties`:
    *   Accept values matching `StringType` enum names.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.