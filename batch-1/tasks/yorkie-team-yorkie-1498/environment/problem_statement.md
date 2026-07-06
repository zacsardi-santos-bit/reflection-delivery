## Description

Currently, webhook behavior parameters — such as how many times a webhook call is retried, how long to wait between retries, and how long to wait before timing out — are configured globally at the server level. This means every project on a server must share the same webhook behavior, with no ability to customize these settings per project.

This is a significant limitation: different projects may have very different reliability requirements or backend webhook server capabilities. A project with a fast, reliable webhook endpoint shouldn't be penalized by conservative global retry settings designed for slower endpoints, and vice versa.

## Expected Behavior

- Each project should be able to independently configure its auth webhook and event webhook retry count, minimum and maximum wait intervals between retries, and request timeout.
- These per-project webhook settings should be editable through the standard project update API.
- The global server configuration should no longer be responsible for these per-project webhook parameters.
- The client deactivation threshold should similarly be managed at the project level rather than set as a global server default.
- The webhook sending infrastructure should be updated so that these per-project options are applied at send time rather than being locked in when the sender is initialized.

## Why This Matters

Without per-project webhook configuration, operators have to choose a single set of retry and timeout parameters that may be suboptimal for all but one of their projects. Moving these settings to the project level gives teams the granular control they need to tune webhook behavior for each project's specific conditions.
