## Description

When building a Telegram bot, it's often useful to support multiple names for the same command — for example, a short abbreviated form like `/s` for `/start`, or a localized version like `/помощь` for `/help`. Currently, the bot command derive macro only supports a single registered name per command, so there's no way to handle these alternative invocations without duplicating handling logic or writing a custom parser.

We need support for command aliases: the ability to declare one or more alternative names for a command variant. These aliases should:
- Be accepted by the parser exactly like the primary command name
- Appear in the generated help message alongside the primary command name (e.g., `/help, /h — Show help`)
- Support non-ASCII characters, enabling localized or multilingual aliases

Additionally, there should be a way to mark aliases as "hidden" — they remain functional for parsing but don't appear in the help message. This is useful when you want shorthand forms to work without cluttering the help listing.

## Expected Behavior

- A single alias can be declared using a dedicated attribute on a command variant.
- Multiple aliases can be declared using an array-style attribute on a command variant.
- Aliases appear in the help text alongside the primary command name, comma-separated.
- A separate "hide aliases" attribute suppresses aliases from the help text while keeping them parseable.
- Applying the hide-aliases attribute to a command that has no aliases should work gracefully (no error, no change in output).
- The hide and alias attributes can be combined: a hidden command with aliases is still parseable but absent from the help text.

## Why This Matters

Supporting command aliases lets bot authors provide both concise shorthand commands and localized alternatives without duplicating business logic. It also enables cleaner help messages by optionally hiding internal shortcut names.
