I'm working on adding support for AI-generated transcripts in Pocket Casts Android. Right now the transcript system doesn't distinguish between transcripts that were generated automatically versus those that were written by humans, so there's no way to gate generated content behind a subscription paywall.

I need to add a boolean flag to the transcript model to mark whether a transcript is AI-generated. When a free user tries to view a generated transcript that actually has content, the player should show a paywall instead of the transcript. Paid subscribers should see generated transcripts just like any other transcript. If the transcript has no content yet, the paywall should not appear even for free users.

When selecting the best transcript for an episode from a list of candidates, the logic should prefer human-authored transcripts over generated ones, but generated-only transcripts should still be usable as a fallback.

On the data fetching side, a new Pocket Casts-specific transcript source should be supported in the show notes response. Transcripts coming from this source should automatically be flagged as generated. Entries that are missing a URL or a type should be filtered out. Also, if an episode has no transcripts from any source at all, the update process should still run with an empty list rather than skipping.

The transcript loading function in the view model no longer needs to accept a parameter indicating whether the transcript view is currently open — that check has been removed and callers should be updated accordingly. The view model also needs to take the subscription manager as a dependency so it can check the user's tier when deciding whether to show the paywall.

A new database migration is needed to store the generated flag persistently.
