I'm building a Telegram bot using the teloxide framework and I want to support multiple names for the same command — for instance, a short form like `/s` as an alternative to `/start`, or a Cyrillic variant like `/помощь` as an alternative to `/help`. Right now, the derive macro only lets me register one name per command variant, so there's no clean way to handle these alternative invocations without duplicating my logic.

I'd like to be able to declare one or more alternative names directly on a command variant using an attribute. When I use a single alternative name, I'd use one attribute form, and when I need multiple alternatives I'd use an array-style form. Both should allow non-ASCII characters for localization purposes.

These alternatives should be accepted by the command parser just like the main command name, and they should also appear in the generated help message alongside the primary command — formatted as a comma-separated list before the description (e.g., `/help, /h — Show help`).

I also want the option to hide alternatives from the help message while keeping them functional for parsing — a "hide aliases" flag on the variant would do this. It should also be possible to combine this with the existing ability to hide an entire command, and applying "hide aliases" to a command that has no alternatives at all should be a no-op.
