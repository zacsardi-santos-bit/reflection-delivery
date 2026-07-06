## Description

This PR improves a tensor comparison debugging utility in three ways: it updates the dimension specification syntax, simplifies the comparison output format, and adds support for automatically navigating into a single engine subdirectory when a wrapper directory is given.

## Changes

### Dimension modifier syntax update

The syntax for annotating how individual tensor dimensions are distributed across parallel workers has been updated to use square brackets instead of parentheses for modifiers (for example, indicating a dimension is sharded with partial reduction). The parsing function now returns a structured result object, and callers access the parsed dimension list via an attribute on that result.

### Simplified comparison output

The comparison output format has been simplified. Previously, all three reported metrics — the relative difference, the maximum absolute difference, and the mean absolute difference — each showed a pass/fail status symbol (checkmark or cross). Now only the primary metric (relative difference) shows the status symbol. The other two metrics are printed without any symbol prefix, reducing visual noise.

### Auto-descent into engine subdirectories

A new utility function is needed that, given a directory and a label string, checks whether the directory directly contains data files. If it does, the directory is returned unchanged. If it does not but has exactly one subdirectory containing data files, that subdirectory is returned automatically. If there are multiple subdirectories with data files, a clear error is raised explaining that multiple subdirectories contain data and the user should specify one directly. If no data files are found anywhere, a clear error is raised explaining that no data was found. When a descent occurs, a log message is emitted that references the label and indicates an auto-descent happened. The main comparison entrypoint must use this function on both input paths so that users can point it at a parent wrapper directory and have it work transparently.

## Why This Matters

Users often store per-engine tensor dumps inside a single parent directory. Without auto-descent, they have to manually specify the exact engine subdirectory. This change lets users pass the wrapper directory directly, and the tool finds the correct subdirectory automatically — or raises a descriptive error if the layout is ambiguous.
