I'm working on a pnpm project and I've noticed that some packages have their build scripts automatically skipped during installation, but I have no good way to find out which ones. I also sometimes explicitly configure packages to skip their builds in my project settings, but there's no command to quickly see that list either.

I'd like a new command that shows me both groups: which packages had their build scripts automatically suppressed during the last install, and which ones I've explicitly told pnpm to skip. When there are automatically suppressed builds, I'd also like some hints about how to either approve them or explicitly mark them as ignored going forward.

The command should handle edge cases gracefully — if no installation has been done yet (no node_modules directory), it should say so rather than erroring out, while still showing any explicitly configured ignores. If there are no automatically suppressed builds, it should clearly indicate that too.
