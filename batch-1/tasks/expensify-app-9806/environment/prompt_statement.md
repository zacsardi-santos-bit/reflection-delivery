I'm cleaning up our staging release tooling and want your help. We track pull requests in a "Staging Deploy Cash" GitHub issue, and right now each PR shows up as a multi-line block with two checkboxes under it, one for QA and one for Accessibility. It's verbose, hard to read, and honestly the accessibility step is redundant in this workflow so I want to drop it entirely and just have a single checkbox per PR (checked means QA-verified, unchecked means not).

So a few things need to change together. The function that builds the release issue body should stop accepting the "accessible PR list" parameter altogether, and instead of a multi-line block per PR, each PR should render as one single line: a checkbox followed by the PR URL. That's it.

Then on the parsing side, when we read that issue body back into structured data objects, the PR records should no longer carry an accessibility-verified field, since it doesn't exist anymore.

And the last bit, the tooling that scans the release issue for deployment blockers currently has a special exception that skips over accessibility checkboxes. Rip that out. With the new format there's no reason for any special-casing, so any unchecked item at all should count as a blocker, full stop. That makes the deploy-blocking logic way more straightforward, oh and it keeps the human-readable checklist simpler too.
