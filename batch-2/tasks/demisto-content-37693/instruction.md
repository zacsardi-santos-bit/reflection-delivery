Fix the Azure AD Connect Health feed integration to ensure it returns a deduplicated list of network endpoint indicators. Implement the necessary changes in the `build_iterator` method of the `Client` class to prevent duplicate entries.

*   Update the `build_iterator` method in the `Client` class to return a list of indicator dictionaries.
    *   Each dictionary must contain at least a 'type' field and a 'value' field.
*   Ensure that when the indicator list is filtered by type 'URL', the resulting set of values contains exactly one entry per unique URL.
*   Ensure that when the indicator list is filtered by type 'DomainGlob', the resulting set of values contains exactly one entry per unique domain glob.
*   Implement deduplication logic in the `build_iterator` method so that each unique endpoint value appears at most once in the returned list.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.