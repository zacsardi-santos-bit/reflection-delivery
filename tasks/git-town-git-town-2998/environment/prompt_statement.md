I'm working on adding an alias configuration dialog to the Git Town setup wizard. The idea is that users should be able to interactively select which Git Town commands they want aliased as shorter git subcommands, so they can type something like "git sync" instead of the full "git town sync".

I need to build out the underlying data structures and logic that will power this dialog. Specifically, I need a way to classify existing aliases into three categories: ones that already point to a Git Town command, ones that point to some other external command, and ones that don't exist at all. There also needs to be logic to determine the result of the dialog — converting each selection state back into the actual alias values to persist.

For the interactive model, I need selection state to be togglable per command entry, with the toggle cycling through states in a sensible way depending on what was originally configured. For example, if a command previously had a custom (non-Git-Town) alias, cycling should eventually bring that state back so the user can restore it. There should also be a way to select all or clear all entries at once.

Additionally, the configuration domain layer needs a typed alias collection type with a method to convert it to plain strings, as well as constructor functions to create this type from raw string input. The function that returns all aliasable commands should return this typed collection rather than a plain slice, and there should be a way to look up a single command by name.

Finally, I need a helper that produces a human-readable summary of which commands are selected — showing "(all)" when everything is aliased, "(none)" when nothing is, and a comma-separated list of command names otherwise.
