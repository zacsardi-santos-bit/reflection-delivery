Update the Gatsby Recipes renderer to improve the plan output by including resource type information and removing ANSI escape codes from diff text. Ensure that each plan item object contains the necessary fields and clean text output.

*   Modify the renderer to include resource type information:
    *   Add a '_type' field to each plan item object, containing a string that specifies the resource type name (e.g., 'File', 'NPMPackage').
    *   Ensure the 'resourceName' field in each plan item object is populated with the resource type name and is not undefined.

*   Ensure the diff text is plain and readable:
    *   Strip all ANSI terminal color and formatting escape sequences from the 'diff' field in each plan item object.
    *   Ensure the diff output is plain text, suitable for non-terminal contexts such as web UIs or plain text processing.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.