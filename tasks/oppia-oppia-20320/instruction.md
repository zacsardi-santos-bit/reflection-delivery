Implement functionality to correctly update translation counts and remove orphaned translations when reverting an exploration or changing its interaction type. Ensure that translation data accurately reflects the current state of the exploration.

*   Update translation counts when an exploration is reverted to a previous version:
    *   Ensure `compute_translation_related_changes_upon_revert` returns a tuple with new `EntityTranslationsModel` objects and a dictionary of translation counts by language.
    *   Create new `EntityTranslationsModel` records for the new version with translations from the reverted version.
    *   Set the version of the new `EntityTranslationsModel` to `current_exploration.version + 1`.
    *   Update `ExplorationOpportunitySummary.translation_counts` to reflect the correct number of translations per language.

*   Remove orphaned translations when content IDs are deleted:
    *   Delete translations for removed content IDs when an exploration's interaction type changes.
    *   Ensure that translations are removed from the new `EntityTranslationsModel` when content IDs are removed during exploration updates.

*   Ensure translation counts are accurate:
    *   Update opportunity models with new translation counts if the exploration is available for contribution.
    *   Use language codes as keys in the `translation_counts` dictionary, with values representing the count of translated contents.

*   Implement `compute_translation_related_changes_upon_revert` in `core/domain/translation_services.py` with the following signature:
    *   `compute_translation_related_changes_upon_revert(current_exploration: exp_domain.Exploration, revert_to_version: int) -> Tuple[List[translation_models.EntityTranslationsModel], Dict[str, int]]`
    *   Retrieve translations from `revert_to_version` and create new models for the current version.
    *   Ensure translation counts reflect the number of valid translations for each language code after updates and reverts.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.