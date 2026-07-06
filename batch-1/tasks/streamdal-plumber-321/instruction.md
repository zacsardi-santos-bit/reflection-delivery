Improve the replay listing output in the batch backend by updating the source information and normalizing the replay type. Ensure that the schema output is cleaned up by removing unnecessary fields.

*   Implement the `listReplays` method in `backends/batch/replays.go` with the following specifications:
    *   Title-case the `type` field in the output (e.g., convert "single" to "Single").
    *   Set the `Source` field to "Collection - <collection_name>" when the replay has a collection field.
    *   Set the `Source` field to "Dead Letter Stage - <stage_name>" when the replay has a stage field and no collection.
    *   Populate the `Destination` field from the `destination.name` in the API response.

*   Update the `ReplayOutput` struct in `backends/batch/replays.go`:
    *   Rename the `Collection` field to `Source`.
    *   Ensure the `Source` field represents either a collection or dead-letter stage source with a descriptive prefix.
    *   Fields should include:
        *   ID string (header:"Replay ID", json:"id")
        *   Name string
        *   Type string (header:"Type", json:"type")
        *   Query string (header:"Query", json:"query")
        *   Source string (header:"Source")
        *   Destination string (header:"Destination Name")
        *   Paused bool (header:"Is Paused", json:"paused")
        *   Status string (header:"Status", json:"status")

*   Define a new `ReplayStage` struct in `backends/batch/replays.go`:
    *   Used to unmarshal the `stage` field from list replays API JSON responses.
    *   Fields should include:
        *   Name string (json:"name")

*   Modify the `SchemaOutput` struct in `backends/batch/schemas.go`:
    *   Remove the `RootType` field.
    *   Ensure the struct only includes:
        *   Name string (header:"Name", json:"name")
        *   ID string (header:"Schema ID", json:"id")
        *   Type string (header:"Type", json:"type")
        *   Archived bool (header:"Is Archived", json:"archived")

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.