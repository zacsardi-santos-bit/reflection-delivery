I'm working on CDS workflow v2 and I want to support reusable job templates — where a single job in a workflow can reference a template that expands into multiple jobs at run time. Right now, the workflow crafting phase doesn't handle these template references at all, so the run never properly builds out the full job graph.

What I need is for the crafting phase to detect when a job's definition points to an external template, fetch that template's job definitions, and inline them into the workflow. The key part is the dependency rewiring: template jobs with no internal dependencies should automatically pick up the upstream dependencies of the placeholder job that referenced them, while any downstream jobs that were waiting on that placeholder should now wait on whichever template jobs have no successors within the template.

This should also handle chaining — if one template's terminal jobs are the entry point for another template's expansion, the full chain should be correctly wired. And if the same template is referenced twice in the same workflow (which would create duplicate job names), the run should fail gracefully with a message explaining the collision rather than producing a broken job graph.

After successful expansion, the workflow run should transition to building status with the full expanded set of jobs in the workflow data.
