I'm deep in the session memory system and hit a cluster of bugs that all need fixing together, so bear with me. First off we've got sessions written in the newer line-delimited format and right now those are completely ignored when we go looking for a previous session to summarize or during memory extraction. The code only knows how to parse the legacy single-document format, so anything line-delimited gets read, sorted, and summarized as if it doesn't exist. Needs to handle both formats.

Also when we pick the most recently updated session we're sorting by the timestamp baked into the filename, but that's wrong. The actual last-updated value lives inside the file and it can differ from the filename date, especially for resumed sessions that got picked back up after an earlier run, so we end up selecting the wrong one. Selection should key off the in-file last-updated timestamp instead.

Then there's the extraction state tracking. Right now if the extraction agent fails to open a session file (permission error, whatever) it still gets recorded as fully processed and never retried, which silently loses history. I want the state to track candidate sessions (the ones we attempted/offered) separately from the ones we actually read successfully, so failures can be retried later.

Related to that, batch selection always grabs the newest sessions, so older sessions that failed to process get perpetually starved and skipped forever. We should rotate older unprocessed sessions into the batches so they get another shot.

Oh and saving summaries: for a session in the new line-delimited format, don't rewrite the whole file, just append a delta record containing only the summary. And handle the concurrent case where another process bumped the session's last-updated timestamp while we were working, the newer timestamp should win and be preserved.

Last thing, the improved memory feature is currently opt-in and I want it enabled by default so users don't have to explicitly configure anything. The whole point is people expect past interactions to be available for memory extraction even if an earlier attempt got interrupted.
