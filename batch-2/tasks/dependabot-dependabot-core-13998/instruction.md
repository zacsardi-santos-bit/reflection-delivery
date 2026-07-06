I'm working on Dependabot's Maven support and running into an issue with dependencies that follow Jenkins' continuous delivery release conventions.

*   When comparing two Maven dependency versions that both contain embedded git commit SHAs, they must be treated as the same version type (compatible upgrade candidates), regardless of how the git SHA portions differ between them.

*   When exactly one of the two versions being compared contains an embedded git commit SHA, they must still be treated as the same version type (compatible upgrade candidates).

*   A valid embedded git commit SHA consists exclusively of hexadecimal characters (0-9, a-f, case-insensitive), optionally with underscores (used by Jenkins to encode uppercase hex characters), must be between 7 and 40 hex characters in length (excluding underscores), and must contain at least one non-numeric hex letter (a-f or A-F). A string that is all digits or uses characters outside the hexadecimal range is NOT a valid git SHA.

*   A git SHA in a version string may be optionally prefixed with the letter 'v' (e.g., 'v08c2b_01b_cf4d'). The presence or absence of this prefix must not affect SHA recognition.

*   A git SHA embedded in a version string may be separated from the numeric version component by either a dot ('.') or a hyphen ('-'). Both delimiter styles must be recognized.

*   When a version's git SHA contains underscores interspersed throughout (Jenkins encoding convention), the SHA must be recognized by reassembling all delimiter-separated parts (stripping dots, hyphens, and underscores) and testing the concatenated result as a potential SHA.

*   A git SHA portion that is fewer than 7 hex characters long must NOT be recognized as a git SHA — such versions must be treated using their full suffix for exact-match comparison.

*   A git SHA portion that is more than 40 hex characters long must NOT be recognized as a git SHA — such versions must be treated using their full suffix for exact-match comparison.

*   A suffix that appears to be a git SHA length but consists only of numeric digits (no a-f letters) must NOT be treated as a git SHA.

*   A suffix that appears to be a git SHA length but contains non-hexadecimal characters (letters outside a-f, such as g-z) must NOT be treated as a git SHA. Two versions with the same invalid suffix must be treated as the same type; two versions with different invalid suffixes must be treated as different types.

*   The version qualifier 'RELEASE' followed immediately by one or more digits (e.g., 'RELEASE802') must be recognized as a stable release marker, not as a pre-release. When the current dependency version is of this form, it must be compatible with stable upgrade candidates (returns true) and incompatible with pre-release upgrade candidates (returns false).

*   The file to modify is: maven/lib/dependabot/maven/shared/shared_version_finder.rb


*   Interface details: NO INTERFACES NEEDED

The tests exercise the existing version type compatibility logic through the established method chain in `Dependabot::Maven::Shared::SharedVersionFinder` (file: `maven/lib/dependabot/maven/shared/shared_version_finder.rb`). No new public methods or classes are introduced. All new git SHA recognition helpers are private internal methods. The required change is to the behavior of the existing private `matches_dependency_version_type?` method and the constants/regex in the same file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.