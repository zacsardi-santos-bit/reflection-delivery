I'm working on improving the test coverage for a notification system. Right now, some notification tests are skipped entirely, and others render notification components in isolation rather than going through the actual notification registration and rendering pipeline. This means the tests don't catch regressions in the conditions that control whether a notification appears.

I need a test utility function that registers specific notifications into the test data store. The utility should accept a registry, an optional set of notification configurations to register, and a flag that controls whether to include a default baseline notification alongside the provided ones. This would make it easy to write tests that exercise the real notification display logic.

I also need to export the existing notification configurations as a named constant so that tests can reference them directly when setting up test scenarios, without having to duplicate the configuration objects.

On top of that, I need to update the existing notification tests to render through the standard notification display component (using the appropriate area identifier) rather than rendering components directly. Specifically:

- The authentication error notification should not appear when the user is not logged in, even if there are unsatisfied permission scopes.
- The specific tag-detection permissions notification should only appear when that is the one and only missing permission. If there are multiple missing permissions, the general authentication error notification should appear instead.
- The tests for the linked-accounts success notification (which are currently skipped) should be re-enabled and updated to render through the notification pipeline, verifying that the notification appears or is suppressed based on module status, account linking, report data availability, dismissal state, and the current view.
