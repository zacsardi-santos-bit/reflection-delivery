## Description

When viewing task logs in the Airflow UI, the structured log output is unnecessarily noisy because the logging framework automatically injects task identity metadata into every single log message. Fields like the task identifier, DAG identifier, run identifier, and similar context fields are identical across every line in a given task run — yet they appear repeated on every single log line, cluttering the view and making it hard to focus on the actual log content.

## Expected Behavior

- Task identity fields that are common to every log line for a task instance should appear only once, in a dedicated "Task Identity" summary block, rather than on every individual log line.
- The "Task Identity" summary block should appear after the log source details section, before the first real log entry.
- Individual log lines should show only fields that vary per line (custom structured fields), not the repeated task identity metadata.
- This cleanup should apply both in the interactive UI log viewer and in the downloadable text version of the logs.
- If no task identity fields are present in the log data, no summary block should be added.

## Why This Matters

Task logs can be long and are often inspected to diagnose problems. Having the same task identity fields repeated on every line creates visual noise that slows down log reading. Consolidating these fields into a single preamble makes logs much easier to scan and understand.
