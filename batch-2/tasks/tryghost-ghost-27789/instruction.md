I'm running into a crash when saving a member record that has labels attached.

*   The onSaving lifecycle hook on the Member model must handle labels that have no name property (e.g. objects containing only an id) by silently skipping them rather than throwing a runtime error.

*   Labels with whitespace-only names must be filtered out during onSaving processing (a name that trims to an empty string is treated as invalid and excluded from the final label set).

*   Case-insensitive duplicate labels must be deduplicated during onSaving: if two labels have names that differ only by case (e.g. 'Newsletter' and 'newsletter'), only the first valid occurrence is kept in the final label list.

*   After onSaving completes, memberModel.get('labels') must return only the valid, deduplicated labels. Given inputs of one valid label, one nameless label, one whitespace-only label, and one case-duplicate, the result must be exactly one label with the original valid name.

*   The existing call to Label.findAll during onSaving must still occur (label normalization against existing persisted labels is preserved).


*   Interface details: Type: Method
Name: onSaving
Location: ghost/core/core/server/models/member.js
Signature: onSaving(model, attrs, options) -> Promise<void>
Description: Bookshelf lifecycle hook called before a Member record is persisted. Handles label normalization: filters out labels with no name or whitespace-only names, deduplicates case-insensitively, and resolves labels against the existing persisted set via Label.findAll. Must not throw when a label object is missing its name property.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.