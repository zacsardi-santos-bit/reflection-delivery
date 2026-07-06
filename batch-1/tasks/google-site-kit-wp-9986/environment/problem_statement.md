## Description

The consent mode setup banner, which encourages users to enable consent mode when they have ads connected, is currently implemented as a standalone component with its own ad-hoc visibility logic. It should be migrated to work within the centralized notification management system so that it benefits from consistent lifecycle management, dismissal tracking, and requirement evaluation like other notifications.

## Expected Behavior

- The consent mode setup notification should be registered in the central notifications registry, with a requirement check that determines when it is eligible to display.
- The notification should be active (eligible to show) only when the user has ads connected but consent mode is not yet enabled.
- The notification should not be active when consent mode is already enabled, even if ads is connected.
- The notification should not be active when ads is not connected, even if consent mode is disabled.
- The component itself should be updated to accept the standard props provided by the notification system, rather than the widget system props it currently uses.
- The component should render nothing when the banner is dismissed or while dismissal state is loading.

## Why This Matters

Migrating this banner to the notification system ensures it is managed consistently with other banners and notifications, allows the notification infrastructure to control its visibility, and simplifies the component's own visibility logic. Users will see the banner only when it is relevant to their setup (ads connected, consent mode not enabled), and the system can correctly suppress it in all other cases.
