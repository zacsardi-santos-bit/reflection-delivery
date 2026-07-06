## Description

CDS workflows should support referencing reusable job templates that define a group of related jobs. When a workflow run is crafted, any job that references such a template should be expanded inline — the template's constituent jobs should be wired into the workflow, replacing the placeholder reference, with all upstream and downstream dependency relationships automatically rewired.

Currently, even if a workflow job points to a template, the crafting phase does not perform this expansion. The template jobs are never instantiated, and dependent downstream jobs have no valid targets to wait on.

## Expected Behavior

- When a job in a workflow references a template, all jobs defined by that template should be injected into the workflow during the crafting phase.
- Template jobs that have no internal dependencies should automatically inherit the upstream dependencies of the job that referenced them.
- Any downstream jobs that were waiting on the template-referencing placeholder should be updated to wait on the template's terminal jobs instead (those that nothing else in the template depends on).
- Multiple templates can be chained in sequence: the terminal jobs of the first template become the entry points of the second, and the final downstream job waits on the second template's terminal jobs.
- If a workflow references the same template more than once (which would produce duplicate job names), the workflow run should be marked as failed with a clear message indicating that the conflicting job names already exist in the workflow.
- A workflow that successfully expands all templates should proceed to building status.

## Why This Matters

Without this capability, teams cannot share common multi-job patterns across workflows. They must manually duplicate job definitions in every workflow that needs them. This feature enables genuine reuse of job groups, reduces duplication, and allows complex multi-stage pipelines to be composed from named, versioned templates.
