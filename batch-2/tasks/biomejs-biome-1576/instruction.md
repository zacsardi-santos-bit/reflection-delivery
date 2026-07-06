Implement a feature in the Prettier-to-Biome migration tool to automatically process the Prettier ignore file. Ensure that ignore patterns are correctly transferred to the Biome configuration, reducing manual intervention during migration.

*   Detect and read the '.prettierignore' file when the migrate command is run with the Prettier flag.
*   Exclude lines starting with '#' (comments) from the migration output.
*   Exclude empty and blank lines from the migration output.
*   Collect non-empty, non-comment lines from '.prettierignore' and:
    *   Preserve their original order.
    *   Place them in the 'ignore' array under the 'formatter' section of the resulting biome.json configuration.
*   In dry-run mode (without the write flag):
    *   Display a diff showing proposed changes to biome.json, including the formatter ignore array.
    *   Inform the user to run with the write option to apply changes.
*   In write mode (with the write flag):
    *   Write the updated biome.json with the formatter ignore array populated from '.prettierignore'.
    *   Display a success message confirming the migration was applied.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.