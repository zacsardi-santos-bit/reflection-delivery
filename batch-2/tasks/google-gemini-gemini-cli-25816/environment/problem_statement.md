## Description

The session memory system has several related issues around how session files are tracked, read, and summarized.

**New session file format is unsupported.** Sessions now stored in a line-delimited format are completely ignored when looking for a previous session to summarize. The system only handles the older single-document format.

**Sessions are sorted by filename, not by actual update time.** When selecting the most recently updated session, the system sorts by the timestamp encoded in the filename. But a session's last-updated timestamp inside the file may differ from the filename date — especially for resumed sessions. This causes the wrong session to be selected.

**No distinction between "offered" and "successfully read" sessions during extraction.** When the memory extraction agent fails to read a session file (due to a permission error or other issue), the system still marks that session as processed and never retries it. The extraction state should record which sessions were candidates and separately which were actually read successfully.

**Older sessions are starved by newer ones.** The extraction batch always fills up with the most recently seen sessions, meaning older sessions that failed processing are perpetually skipped in favor of newer ones.

**Memory feature is disabled by default.** The improved memory experience requires users to explicitly opt in. It should be enabled by default.

## Expected Behavior

- Sessions in the new line-delimited format are read, sorted, and summarized correctly
- Session selection is based on the actual last-updated time stored in the file, not the filename
- The extraction state separately records candidate sessions (attempted) and processed sessions (successfully read)
- Older unprocessed sessions are rotated into extraction batches to prevent starvation
- The improved memory feature is active by default without requiring explicit configuration

## Why This Matters

Conversation history is silently lost when the system ignores new-format sessions, picks the wrong session to summarize, or permanently skips sessions it failed to extract. Users expect their past interactions to be available for memory extraction, even if an earlier attempt was interrupted.
