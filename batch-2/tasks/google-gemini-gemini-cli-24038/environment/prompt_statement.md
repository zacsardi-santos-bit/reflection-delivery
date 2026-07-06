I'm dealing with our sandbox security layer, the bit that decides which file paths are allowed vs forbidden when we run sandboxed commands, and I've got three things to untangle.

First up, forbidden paths shouldn't be a static array passed in when the sandbox manager gets constructed. Some of these paths come from reading project config off disk, which is async and can happen way later than startup, so forcing early resolution is wasteful and rigid. I want to hand it a deferred function that returns a promise of paths and only actually call it when the sandbox goes to prepare a command. The config layer needs to wire this up so forbidden paths aren't fetched during init at all, only lazily when a command's being prepared.

Second, the allowed/forbidden conflict handling is fragile. Right now if a path shows up in both lists it gets added to the allow list and then denied, which is this ordering-dependent mess. What I actually want is forbidden always wins, so a conflicting path gets stripped out of the allowed list entirely and never shows up in both.

Third, path comparison ignores case-insensitive filesystems. On macOS and Windows two paths differing only in case should count as the same for dedup and conflict detection, but on Linux case stays significant. For this I need a utility that normalizes a path to an "identity" for comparison, so stripping trailing slashes and normalizing separators, then lowercasing only on the case-insensitive platforms. Oh and the path sanitization helper should return an empty array when no paths are given instead of undefined or null.

Net effect I'm after: security rules evaluated at the right time, no startup overhead resolving paths we might not need, no ambiguous allow-then-deny, and correct behavior across platforms.
