## Description

When an exploration is reverted to a previous version, the translation counts are not being properly updated. Additionally, when an exploration's interaction type is changed (such as from a Continue button to Multiple Choice Input), translations for content IDs that no longer exist in the new version are not being removed from the translation models.

This causes inconsistent translation data where:
1. The translation counts displayed in the opportunity summary don't reflect the actual translations available for the reverted exploration version
2. Translation models contain orphaned translations for content that no longer exists in the exploration

## Expected Behavior

- When `revert_exploration` is called to revert an exploration from the current version back to a previous version, the translation counts should be updated to match the translations that existed for the target version
- New `EntityTranslationsModel` records should be created for the new version containing only the translations from the version being reverted to
- The `ExplorationOpportunitySummary.translation_counts` should accurately reflect the number of translated content items per language
- When updating an exploration where content IDs are removed (e.g., changing interaction type removes certain customization content IDs), the corresponding translations should be deleted from the new translation models

## Current Behavior

- Reverting an exploration does not update the translation-related models at all
- Translation counts remain unchanged after a revert, showing incorrect values
- When content IDs are removed during exploration updates, their associated translations are retained in the new translation models
- The opportunity summary shows stale translation counts that don't match the actual state of translations
