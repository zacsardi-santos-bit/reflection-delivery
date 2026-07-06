Implement support for AI-generated transcripts in Pocket Casts by adding a subscription-based access control. Ensure that transcripts can be flagged as AI-generated and that free users encounter a paywall when accessing these transcripts, while subscribers can view them freely.

*   Update the Transcript data class:
    *   Add an `isGenerated: Boolean` parameter to indicate AI-generated transcripts.
    *   Ensure all existing constructors explicitly set `isGenerated` (default to false for human-authored).
*   Modify the TranscriptsManager interface:
    *   Correct the method name to `observeTranscriptForEpisode` and ensure it returns a Flow of nullable Transcript.
    *   Implement `findBestTranscript` to prioritize human-authored transcripts over generated ones.
*   Adjust the TranscriptViewModel:
    *   Remove the `isTranscriptViewOpen` parameter from `parseAndLoadTranscript`. Use the signature: `fun parseAndLoadTranscript(pulledToRefresh: Boolean = false, retryOnFail: Boolean = false)`.
    *   Add a `SubscriptionManager` parameter to the constructor.
*   Update UiState.TranscriptLoaded:
    *   Include a `showPaywall: Boolean` field.
    *   Set `showPaywall` to true if the transcript is generated, has content, and the user's subscription tier is NONE. Set to false for PLUS or PATRON tiers, or if the transcript is empty.
*   Enhance ShowNotesEpisode:
    *   Add a `pocketCastsTranscripts: List<ShowNotesTranscript>?` field.
    *   Convert entries from `pocketCastsTranscripts` to Transcript objects with `isGenerated = true`.
    *   Convert entries from `transcripts` to Transcript objects with `isGenerated = false`.
    *   Filter out entries missing a URL or type.
    *   Ensure `updateTranscripts` is called with an empty list if both fields are null.
*   Implement database migration:
    *   Define `AppDatabase.MIGRATION_110_111` as a companion object member.
    *   Include this migration in the database construction chain.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.