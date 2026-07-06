I'm hitting a data-loss thing in Dolt and want to fix it. When I check out a different branch, tables I've explicitly marked as ignored can get silently overwritten by the target branch's version. I use the ignore system to manage local-only stuff I never want to commit (environment-specific or runtime data), and a plain branch switch can wipe it out with zero warning, which is really surprising and disruptive.

What I want is a way to make checkout safer here. Give me a flag I can opt into that aborts the checkout if any of my locally-present ignored tables would be overwritten by the switch, and the error needs to actually name the specific tables at risk, not just fail vaguely. Also add a companion flag that explicitly says I'm fine with the overwrite (this keeps today's behavior but makes intent clear in scripts). These two should be mutually exclusive, so passing both in the same invocation should be rejected as a config error.

The protection's gotta be precise though. It should only block when the ignored table actually differs between the branches. If the table's identical on both sides there's nothing to protect, so proceed. Same if the target branch doesn't have the table at all, or if the table only exists locally and isn't on the target, no overwrite is happening so let it through. And creating a brand-new branch from the current HEAD should never be blocked since no overwrite can occur there.

Oh and one edge case that matters: combining my protection flag with the force flag (the one that discards working-set changes) should not bypass the ignored-table check. Force and the ignore protection need to be independent of each other.

All of this should work both from the command line and through the SQL stored procedure interface for checkout.
