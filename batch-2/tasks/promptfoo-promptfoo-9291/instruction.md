I'm working on improving the HTML report that gets generated when an evaluation run is exported.

*   The HTML output template file (src/tableOutput.html) must reference cell output text via cell.text (not a flat cell value), grading reason via cell.reason, and error message via cell.error, each escaped to prevent injection.

*   The template must include a report description rendered via the config.description field with a fallback default value of 'Eval Output', escaped for safety.

*   Each output cell element in the template must carry a data-output-cell attribute set to 'true' and a data-status attribute containing the cell's status value escaped. Variable cells (one per input variable per row) must carry a data-variable-name attribute containing the variable name escaped.

*   The template must include a data-search attribute on output cells whose value begins with the cell's status label (escaped) and ends with the cell's error value (escaped), forming a combined search string.

*   The template must include a status pill element whose CSS class incorporates the cell's status value: 'status-pill {{ cell.status | escape }}'.

*   The template must include elements for report-level search (data-report-search), status filtering (data-status-filter), and a visible-count indicator (data-visible-count).

*   The template must include elements for a collapsible detail drawer using data-open-detail, data-detail-drawer, and data-detail-template attributes, and must call appendVariableDetails(trigger) in JavaScript to load variable details on demand.

*   The template must NOT pre-render inline variable iteration (no Jinja/Nunjucks loop over cell variables); variable details must be loaded lazily via the appendVariableDetails JavaScript function.

*   When writeOutput renders HTML, the output must include the eval description, a summary section with '<p class="metric-label">Total Results</p>' and a '<p class="metric-value">' element showing the total result count, and a pass-rate percentage formatted to one decimal place (e.g. '50.0%').

*   The rendered HTML must display status labels '>PASS<' and '>FAIL<' inline for each result, and score values formatted as 'Score X.XX' (two decimal places).

*   The rendered HTML must include the response output text, the grading reason text, and a 'View detail' link for each result row.

*   Detail drawer titles must follow the pattern 'Result detail - row N, prompt N' (1-indexed), and drawers must include 'Prompt' and 'Variables' section headers.

*   The rendered HTML must include 'No rows match the current search and status filters.' as the empty-state message when no rows are visible.

*   When a result has success=false regardless of its specific failure reason (including when no explicit failure reason is set), the cell must carry data-status="fail" and display '>FAIL<'.

*   When a result has an error failure reason with an error string, the cell's data-search attribute value must be: '{statusLabel} {score formatted to 2 decimal places} {error} {gradingReason} {error}'. For example, a result with statusLabel=ERROR, score=0, error='provider timed out', and gradingReason='Evaluation failed' must produce data-search="ERROR 0.00 provider timed out Evaluation failed provider timed out".

*   For error results, the rendered HTML must include data-variable-name='{varName}' for each variable but must NOT contain '<span class="detail-variable-name">' (no pre-rendered variable detail spans).


*   Interface details: Type: File
Name: tableOutput.html
Location: src/tableOutput.html
Description: Nunjucks/Jinja2 HTML template rendered by writeOutput when producing HTML reports. Must be updated to use a structured cell object with named fields instead of a flat cell value. The template context includes a config object and an array of cell objects per row.

Required template variables and attributes:
- config.description — rendered with a default fallback of 'Eval Output' and escaped
- cell.text — the response output text, escaped
- cell.reason — the grading reason string, escaped
- cell.error — the error message string, escaped
- cell.status — lowercase status value ('pass', 'fail', 'error'), escaped; used in data-status attribute and CSS class
- cell.statusLabel — uppercase display label ('PASS', 'FAIL', 'ERROR'), escaped; used in data-search and inline display
- cell.name — the variable name for this cell's row, escaped; used in data-variable-name attribute

Required HTML data attributes in the template:
- data-output-cell="true" on each result cell element
- data-status="{{ cell.status | escape }}" on each result cell
- data-variable-name="{{ cell.name | escape }}" on variable name elements
- data-search attribute that starts with {{ cell.statusLabel | escape }} and ends with {{ cell.error | escape }}"
- data-report-search on the search input/container element
- data-status-filter on the status filter element
- data-visible-count on the count display element
- data-open-detail on the detail trigger element
- data-detail-drawer on the drawer container element
- data-detail-template on the detail template element
- status-pill {{ cell.status | escape }} as a CSS class string

Required JavaScript:
- appendVariableDetails(trigger); must appear in an inline script block for lazy-loading variable details

Prohibited template patterns:
- Must NOT contain {% for variable in cell.variables %} (variables are loaded lazily, not pre-rendered)
- Must NOT render <span class="detail-variable-name"> inline

Required summary section:
- <p class="metric-label">Total Results</p> and a corresponding <p class="metric-value"> element with the total count
- A pass-rate percentage value formatted to one decimal place (e.g. '50.0%')
- 'No rows match the current search and status filters.' as the empty-state message

Required detail drawer content per row:
- 'View detail' link text
- Drawer title following the pattern 'Result detail - row N, prompt N' (1-indexed)
- 'Prompt' and 'Variables' section header labels inside the drawer


Type: Function
Name: writeOutput
Location: src/util/output.ts
Signature: writeOutput(outputPath: string, evalRecord: Eval, shareableUrl: string | null) => Promise<void>
Description: Existing function that writes evaluation results to a file. When outputPath ends in .html, it renders src/tableOutput.html with a context that includes config (with description field), summary statistics (total count, pass rate), and per-result cell objects. Must be updated to build cell objects with the following shape before template rendering:
  - text: string — the response output
  - reason: string — the grading reason (empty string if absent)
  - error: string — the error message (empty string if absent)
  - status: 'pass' | 'fail' | 'error' — derived from success flag and failure reason
  - statusLabel: 'PASS' | 'FAIL' | 'ERROR' — uppercase version of status
  - name: string — the variable name for this cell's row
  - score: number — the numeric score

Status derivation rules:
  - If failureReason is the error type: status = 'error', statusLabel = 'ERROR'
  - If success is false for any other reason (including no explicit failure reason): status = 'fail', statusLabel = 'FAIL'
  - If success is true: status = 'pass', statusLabel = 'PASS'

data-search value format (rendered into HTML):
  '{statusLabel} {score.toFixed(2)} {error} {reason} {error}'
  Example: 'ERROR 0.00 provider timed out Evaluation failed provider timed out'


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.