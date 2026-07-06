I'm hacking on our CI dependency bot, the thing that auto-bumps important package versions, and right now it's got one global cooldown for everything: if a version dropped in the last four days (96 hours) we skip it. Fine as a default but too rigid. Sometimes a critical fix lands and I want to say "for this one package, only wait 6 hours" without dropping the safety net everywhere else. So I want per-package cooldown overrides driven by annotations in our config file, plus a few helpers to make it work.

First, a duration parser that turns human strings like "12 hours", "1 day", "30 minutes" into a number of hours. Anything it can't understand (ISO timestamps, random garbage, empty string) should come back with a sentinel failure value rather than throwing.

Second, a function that takes the config file content and pulls out the manually added per-package overrides, handing back a dict mapping package name to cooldown hours. It should skip entries that just disable a package outright rather than giving a duration, those aren't overrides I care about here.

Third, update the existing cooldown check so it takes an optional per-package cooldown arg. When that's passed, use it instead of the global window; when it's not, fall back to the usual 96-hour (4-day) behaviour so nothing changes for the untouched packages.

Fourth, a removal function that strips a given package's override entry from the config, including any expiry reminder comments sitting right above it. It's gotta hit all the relevant sections of the file consistently, be idempotent (safe to call over and over), and if the package has no override entry just return the file unchanged and leave everything else alone. The upgrade helpers live alongside the rest of the CI tooling, so wire these in there.
