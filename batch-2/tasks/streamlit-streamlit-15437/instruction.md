I'd like Streamlit to show a short recommendation at startup when the official agent skills aren't yet installed.

*   The `are_skills_installed` function in `lib/streamlit/web/skills.py` must return `True` if the entry named `_GLOBAL_SKILL_NAME` exists (as a regular directory, a copied directory, or a symlink) in any project-local or global agent skills target directory, and `False` if it is found in none of them.

*   When `are_skills_installed` encounters an `OSError` or `RuntimeError` while resolving project root or global target directories and no installed skill has been detected in any directory checked so far, it must return `False`.

*   When `are_skills_installed` encounters an error resolving global target directories, it must still evaluate any project-local target directories that were already collected before the error occurred.

*   When `are_skills_installed` encounters an error resolving the project root (so no project target directories can be determined), it must still check global target directories.

*   When `are_skills_installed` encounters an error resolving the project-local target directories, it must still check global target directories.

*   The `_maybe_print_skills_recommendation` function must exist in `lib/streamlit/web/bootstrap.py` and must be called during server start-up.

*   When `server.headless` is `True`, `_maybe_print_skills_recommendation` must produce no output.

*   When `logger.hideWelcomeMessage` is `True`, `_maybe_print_skills_recommendation` must produce no output and must not invoke `are_skills_installed` at all (i.e., it short-circuits before the installation check).

*   When `are_skills_installed` returns `True`, `_maybe_print_skills_recommendation` must produce no output.

*   When the server is not headless, the welcome message is not hidden, and `are_skills_installed` returns `False`, `_maybe_print_skills_recommendation` must print output that contains all three of the following strings: 'Help agents write better Streamlit apps?', 'streamlit skills', and 'Install the official Streamlit skills'.


*   Interface details: Type: Function
Name: are_skills_installed
Location: lib/streamlit/web/skills.py
Signature: are_skills_installed() -> bool
Description: Returns True if the bundled skill (the entry named _GLOBAL_SKILL_NAME) is present as a directory, copied directory, or symlink in any project-local or global agent skills target directory. Returns False if no skill is found in any checked directory. Handles OSError and RuntimeError from individual sub-lookups gracefully: if resolving one category of directories fails the function still checks the others, and only returns False (without raising) if no installed skill can be detected after all available lookups are attempted.

Type: Function
Name: _maybe_print_skills_recommendation
Location: lib/streamlit/web/bootstrap.py
Signature: _maybe_print_skills_recommendation() -> None
Description: Called during server start-up. Prints a skills installation recommendation to the terminal when the server is not in headless mode, the welcome message is not hidden, and are_skills_installed() returns False. When server.headless is True, prints nothing. When logger.hideWelcomeMessage is True, prints nothing and short-circuits before calling are_skills_installed(). When are_skills_installed() returns True, prints nothing. When all conditions allow it, the printed output must contain the strings "Help agents write better Streamlit apps?", "streamlit skills", and "Install the official Streamlit skills".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.