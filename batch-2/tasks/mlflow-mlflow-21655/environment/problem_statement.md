# Issue: Enhance the Issue Discovery Pipeline with Category Filtering, Cost Tracking, and Run ID Support

## Description

The issue discovery pipeline for GenAI applications needs several improvements to make it more useful and observable in production workflows.

**Category filtering**: Currently, the pipeline does not let users control which issue categories are recognized. When the AI model assigns category tags to discovered issues, it can produce arbitrary labels that don't match the user's taxonomy. Users need a way to supply a fixed list of valid categories so that only recognized categories appear in the results.

**Cost tracking**: Users have no visibility into how much LLM resource consumption the discovery process incurs. The result object should include the total estimated cost (in USD) of all LLM calls made during the run.

**Reusable run ID**: The discovery pipeline always creates a new MLflow run. Users should be able to supply an existing run ID to attach discovery results to a tracking run they already created.

**Run tagging**: Runs created by the discovery pipeline should be tagged to identify them as issue detection runs, making them easy to distinguish from general evaluation runs.

**Summary formatting**: The output summary text currently starts with a markdown heading that makes it awkward to embed within other UI contexts. The heading prefix should be removed.

## Expected Behavior

- Users can pass a list of valid categories to the discovery function, and only categories in that list will appear on discovered issues.
- The discovery result object exposes the total LLM cost incurred. When no traces are analyzed, the cost is zero. When traces are processed, the cost is a positive number.
- Users can pass an existing run ID; the result's triage run ID matches that ID and the run remains accessible afterward.
- Runs started by the discovery pipeline are tagged to indicate they are issue detection runs.
- The summary returned for zero issues reads as plain text (e.g., "Analyzed N traces. No issues found.") without a markdown heading.

## Why This Matters

These improvements give practitioners control over issue taxonomy, cost visibility, and tighter integration with existing MLflow tracking workflows.
