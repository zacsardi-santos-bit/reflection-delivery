I'm working on improving the readability of task logs in the Airflow web UI.

*   The tiContextFields export must be an array of exactly 6 strings — "ti_id", "dag_id", "task_id", "run_id", "try_number", "map_index" — and must have length exactly 6.

*   When renderStructuredLog renders a structured log message in jsx mode, it must skip all fields listed in tiContextFields and not include them as per-line attributes. Non-TI structured fields (fields not in tiContextFields) must still render normally.

*   renderTIContextPreamble in text mode must return a string containing key=value pairs (joined by spaces) for each tiContextField present in the context object. If a label argument is provided, the string must be prefixed with "${label} ". If no label is provided, no prefix is added.

*   renderTIContextPreamble in jsx mode must return a JSX element that renders the label text (if provided) followed by key=value entries for each tiContextField present in the context. Each field key must render in its own element; the value must appear adjacent to an "=" character such that the full text content reads "key=value". If the context is empty and a label is provided, the element must render the label only with no field entries.

*   renderTIContextPreamble must only render fields that are both in tiContextFields and present in the supplied context object; fields not in the context must be omitted entirely.

*   getDownloadText must, when any log line contains TI context fields, insert a "Task Identity" preamble line (produced by renderTIContextPreamble in text mode with label "Task Identity") into the output array immediately after the first "::endgroup::" line and before the first real log content line.

*   Individual log lines produced by getDownloadText must not contain the substrings "ti_id=", "dag_id=", or "run_id=".

*   getDownloadText must not insert any Task Identity preamble when no TI context fields are present in any log line.

*   The UI log viewer must render a visible "Task Identity" block (containing the "Task Identity" label and task identity field values) positioned after the "Log message source details" group header in DOM order.

*   Individual log line elements in the UI must not carry data attributes for TI context fields such as ti_id, dag_id, or run_id.

*   A mock HTTP handler returning structured log data for the ti_context task must be added. The log response content must include structured entries carrying ti_id "01951900-16f6-7c1c-ae66-91bdfe9e0cfd" along with other TI context fields. The handler must also include a source-details group marker (::group::Log message source details / ::endgroup::) before the log entries.


*   Interface details: Type: Constant
Name: tiContextFields
Location: airflow-core/src/airflow/ui/src/components/renderStructuredLog.tsx
Signature: export const tiContextFields: string[]
Description: Exported array of exactly 6 field name strings identifying task instance context fields that are injected on every log line. Must contain exactly: "ti_id", "dag_id", "task_id", "run_id", "try_number", "map_index". Length must be exactly 6.

---

Type: Function
Name: renderTIContextPreamble
Location: airflow-core/src/airflow/ui/src/components/renderStructuredLog.tsx
Signature: renderTIContextPreamble(context: Record<string, unknown>, renderingMode?: "jsx" | "text", label?: string): JSX.Element | string
Description: Renders a preamble summarizing task identity context fields. The default renderingMode is "jsx". Only fields from tiContextFields that exist in the context object are rendered.
  - In "text" mode: returns a string. If label is provided, the string starts with "${label} " followed by space-joined "key=value" pairs. If label is omitted, returns only the "key=value" pairs.
  - In "jsx" mode: returns a JSX element. If label is provided, the label text is rendered as a child element. Each present field is rendered with its key in its own span and the value as a text node adjacent to "=", so the container text content reads "key=value". If context is empty and label is provided, only the label is rendered with no field entries.

---

Type: Mock Handler
Name: ti_context mock handlers
Location: airflow-core/src/airflow/ui/src/mocks/handlers/log.ts
Description: Two mock HTTP GET handlers must be added:
  1. GET /api/v2/dags/log_grouping/dagRuns/manual__2025-02-18T12:19/taskInstances/ti_context/-1 — returns a task instance object with task_id "ti_context" and dag_run_id "manual__2025-02-18T12:19".
  2. GET /api/v2/dags/log_grouping/dagRuns/manual__2025-02-18T12:19/taskInstances/ti_context/logs/1 — returns a log response whose content array begins with a source-details group (event "::group::Log message source details" with sources array containing "/home/airflow/logs/dag_id=log_grouping/run_id=manual__2025-02-18T12:19/task_id=ti_context/attempt=1.log", followed by event "::endgroup::"), then structured log entries that include ti_id "01951900-16f6-7c1c-ae66-91bdfe9e0cfd", dag_id "log_grouping", task_id "ti_context", run_id "manual__2025-02-18T12:19", try_number 1, and map_index -1.

---

Note: The following existing functions must also be modified (they are not new interfaces, but their behavior must change):

- renderStructuredLog (renderStructuredLog.tsx): Must skip all fields in tiContextFields when building per-line structured attributes in its output. Non-TI fields must continue to render normally.
- getDownloadText (airflow-core/src/airflow/ui/src/pages/TaskInstance/Logs/utils.ts): Must detect TI context fields in log data, insert a "Task Identity" preamble line (using renderTIContextPreamble in "text" mode with label "Task Identity") after the first "::endgroup::" line, and exclude TI context fields from individual log line output.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.