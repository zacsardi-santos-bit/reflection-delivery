Improve the Flutter tool's crash handling experience by implementing enhanced error messaging and fixing a bug related to Android compatibility settings. Ensure that developers receive useful links and guidance when a crash occurs, and handle missing manifest sections gracefully.

*   Update `FlutterManifest` class:
    *   Ensure `usesAndroidX` returns `false` when the 'module' section is absent in the flutter descriptor.
    *   Ensure `usesAndroidX` returns `true` when 'flutter.module.androidX' is set to `true`.

*   Modify crash reporting behavior:
    *   Emit crash reporting messages at the trace/verbose level using `printTrace`.
    *   Ensure 'Crash report sent' message does not appear if no report is sent.

*   Implement `GitHubTemplateCreator` class:
    *   Make it injectable via the Flutter service locator under the `GitHubTemplateCreator` type key.
    *   Implement `toolCrashSimilarIssuesGitHubURL(String errorString)`:
        *   Return a shortened URL if a POST to git.io returns HTTP 201 with a Location header.
        *   Return the full URL 'https://github.com/flutter/flutter/issues?q=is%3Aissue+<Uri.encodeQueryComponent(errorString)>' if shortening fails.
    *   Implement `toolCrashIssueTemplateGitHubURL(String command, String errorString, String exception, StackTrace stackTrace, String doctorText)`:
        *   Return a shortened URL if git.io shortening succeeds.
        *   Return the full URL 'https://github.com/flutter/flutter/issues/new' with encoded parameters if shortening fails.

*   Format the issue template body:
    *   Use the specified format for command, steps to reproduce, logs, and Flutter application metadata.
    *   Include metadata fields with no leading indentation.

*   Update Flutter runner crash handling:
    *   Emit error output: 'Oops; flutter has exited unexpectedly: "<errorString>".'
    *   Emit error output: 'A crash report has been written to <file_path>.'
    *   Emit status output: similar issues URL, 'https://flutter.dev/docs/resources/bug-reports', and new issue template URL.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.