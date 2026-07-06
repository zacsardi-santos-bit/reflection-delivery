Update the release workflow to use short, human-readable package names in the package-selection dropdown. Modify the validation script to expand these short names into full directory paths before comparison, ensuring it correctly identifies all package directories on disk.

*   Update the release workflow file at `.github/workflows/_release.yml`:
    *   List dropdown options using only short package names, without any path prefix.
    *   Ensure top-level packages (core, langchain, langchain_v1, text-splitters, standard-tests, model-profiles) appear as short names.
    *   Ensure partner packages (e.g. openai, anthropic, groq) also appear as short names.

*   Modify the validation script to handle path expansion:
    *   Expand top-level package names to 'libs/<name>'.
    *   Expand partner package names to 'libs/partners/<name>'.
    *   Ensure the expanded paths match the package directories under `libs/` exactly, with no missing or extra entries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.