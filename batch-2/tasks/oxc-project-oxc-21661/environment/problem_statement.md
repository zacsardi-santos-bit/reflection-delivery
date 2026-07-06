## Description

The formatter does not validate configuration options that are specified inside an overrides section. When an invalid or conflicting option appears at the top level of a configuration file, the formatter correctly reports a parse error and exits immediately. However, when the same invalid option (such as a line width value that is out of the allowed range) or a conflicting pair of options appears only within the overrides block, the formatter silently accepts it with no diagnostic output, leaving the user with no indication of what went wrong.

## Expected Behavior

- When a configuration's overrides section resolves to an invalid setting for a specific file, the formatter should report a clear per-file error identifying both the affected file and the exact reason the configuration is invalid.
- When two mutually exclusive import-sorting options are both enabled after merging overrides, the formatter should report that combination as a configuration conflict.
- Invalid override-resolved configurations should result in an exit code of 2 and diagnostic output in the same structured error format used for other per-file errors.

## Why This Matters

Users who write per-file formatting overrides in their configuration files can easily introduce invalid settings without realizing it, especially when the override only changes one field while another field inherited from the root creates a conflict. Without validation of resolved configurations, these errors are silently ignored, causing confusing or incorrect formatting behavior.
