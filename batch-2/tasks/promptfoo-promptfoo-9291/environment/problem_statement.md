## Description

The static HTML report generated from an evaluation run is too bare-bones for practical use. It currently renders each result as a single flat string, with no structured metadata, no summary statistics, and no way to search or filter results. The template also does not properly separate the different parts of a result (output text, score, grading reason, error message), making it hard to skim or drill into individual outcomes.

## Expected Behavior

- The report header should show key summary metrics: total result count and overall pass rate as a percentage.
- Each result cell should surface the output text, numeric score (formatted to two decimal places), grading reason, and error message as distinct, individually escaped fields.
- The report should include a search bar and status filter so users can quickly find passing, failing, or error results.
- Clicking on a result should open a detail panel showing the prompt, the input variables, and other per-result information. Variable details should be loaded on demand rather than pre-rendered into the HTML, keeping large reports fast.
- Any failed result — regardless of the specific internal reason for the failure — should be consistently classified and displayed as a failure.
- The error message for a failed result should appear in a searchable index field alongside the score and grading reason, so users can find results by error text.
- A visible empty-state message should appear when no rows match the current search or filter criteria.
- All user-provided content in the template must be properly escaped to prevent injection.

## Why This Matters

Evaluations can produce hundreds of results across many variables and prompts. Without summary stats, search, and detail drill-down, the HTML output is difficult to use for analysis or debugging. Improving the report structure makes it practical to review evaluation runs directly from the exported file without needing the full application UI.
