Implement JSON serialization support for the CLI application's command structure using the Go `encoding/json` package. Ensure that all relevant command metadata, flags, and arguments are included in the JSON output, adhering to the specified field names and structures.

Requirements:

*   Update the `Command` struct in `command.go` to support JSON marshaling.
    *   Serialize fields with these JSON keys: `name`, `aliases`, `usage`, `usageText`, `argsUsage`, `version`, `description`, `defaultCommand`, `category`, `commands`, `flags`, `hideHelp`, `hideHelpCommand`, `hideVersion`, `hidden`, `authors`, `copyright`, `metadata`, `sliceFlagSeparator`, `disableSliceFlagSeparator`, `useShortOptionHandling`, `suggest`, `allowExtFlags`, `skipFlagParsing`, `prefixMatchCommands`, `mutuallyExclusiveFlags`, `arguments`, `readArgsFromStdin`.
    *   Exclude fields with unexportable values from JSON output.

*   Ensure the `commands` field serializes as an array of `Command` objects or null if empty.

*   Update the `FlagBase[T, C, VC]` struct in `flag_impl.go` to support JSON marshaling.
    *   Serialize fields with these JSON keys: `name`, `category`, `defaultText`, `usage`, `required`, `hidden`, `persistent`, `defaultValue`, `aliases`, `takesFileArg`, `config`, `onlyOnce`.
    *   Exclude fields `Sources`, `Destination`, `Action`, and `Validator` from JSON output.

*   Ensure `StringFlag` and `BoolFlag` instances serialize with appropriate `config` fields.
    *   `StringFlag`: `config` should be `{"TrimSpace": <bool>}` and `defaultValue` as a string.
    *   `BoolFlag`: `config` should be `{"Count": <null or int>}` and `defaultValue` as a boolean.

*   Update the `ArgumentBase[T, C, VC]` struct in `args.go` to support JSON marshaling.
    *   Serialize fields with these JSON keys: `name`, `value`, `usageText`, `minTimes`, `maxTimes`, `config`.
    *   Exclude fields `Destination` and `Values` from JSON output.

*   Ensure the `arguments` field serializes as an array of `Argument` objects or null if empty.

*   Implement mixed-type serialization for the `authors` field.
    *   Plain string authors serialize as JSON strings.
    *   Structured authors serialize as JSON objects with `Name` and `Address` fields.

*   Serialize null/empty slices for fields like `aliases`, `commands`, `flags`, `arguments`, `mutuallyExclusiveFlags`, and `authors` as JSON null.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.