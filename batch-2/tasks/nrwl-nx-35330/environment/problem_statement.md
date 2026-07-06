I'm migrating an Nx workspace from the legacy ESLint config format to the new flat config format with the built-in conversion tool, and I keep hitting a bunch of correctness bugs in the output that make the migrated config behave differently than before and also mess up build caching.

First off, every generated flat config gets an extra block jammed at the top that ignores the usual build output directories, but that wasn't in my original configs and shouldn't be auto-injected at all, so please drop that default ignore block entirely.

Second, some of my project configs use the extends field to point at a neighboring base config file without spelling out the file extension (the old format let you omit it). After migration the converter doesn't recognize those as JSON configs so they end up routed through a compatibility shim instead of becoming a direct flat config import. I want extensionless base references treated as JSON configs and turned into proper direct imports.

Third, a few configs use ignore patterns that pair a broad exclusion (ignore a whole folder) with a targeted negation to keep one specific file un-ignored. Right now those negated patterns get dropped, so the converted config ignores more than it should. Those negations paired with the broader pattern need to survive into the output.

Fourth, in ESM output the parsers referenced inside overrides come out as dynamic inline import expressions instead of static top-level imports. I need them as static top-level imports so the module's exported bindings resolve correctly (dynamic inline stuff breaks modules that expose their API through top-level exports).

Finally, after conversion the old ESLint config and ignore filenames are still floating around in the input lists of both the workspace configuration and the individual project configuration files. Those stale references keep the build cache tracking files that don't exist anymore, so all such input entries should get rewritten to point at the new flat config filenames, and when multiple old names map to the same new name the resulting duplicates should collapse into a single entry.
