I'd like to add support for terminal hyperlinks to fd's output. Many modern terminal emulators can render clickable links using special escape codes, and it would be great if fd could wrap output file paths in these hyperlinks so users can click directly on results to open the corresponding files.

The feature should be controlled by a new option that accepts values like "always", "never", and "auto", with "never" as the default. When set to "always", every output path should be wrapped in the standard terminal hyperlink escape codes, forming a file URL that includes the system hostname and the absolute path to the file. On systems where a hostname isn't available, the hostname portion of the URL should simply be omitted. The "auto" mode should activate hyperlinks only when other terminal formatting is also enabled.

This is entirely opt-in, so existing behavior should be completely unchanged unless the user explicitly requests hyperlinks.
