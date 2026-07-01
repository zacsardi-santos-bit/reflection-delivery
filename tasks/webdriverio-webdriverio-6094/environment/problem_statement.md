## Description

The devtools package currently requires users to place browser launch settings — such as headless mode, ignoring default browser arguments, and viewport configuration — inside browser-vendor-specific capability namespaces or at the top level of the capabilities object. This is inconsistent: the same kind of setting (headless mode) goes in different places depending on which browser you're targeting, and devtools-specific options are mixed together with standard WebDriver browser capabilities.

We should introduce a dedicated capability namespace for WebdriverIO devtools options so that all devtools-specific browser launch settings can be placed together in one clear location, regardless of which browser is being used.

## Expected Behavior

- A new dedicated capability namespace should be supported for specifying devtools-specific launch options such as headless mode, default argument overriding (as either a boolean or a list of specific arguments to ignore), and default viewport dimensions.
- Options provided in this dedicated namespace should take effect with higher priority than options in vendor-specific namespaces.
- The old configuration formats (specifying these options directly in vendor-specific capability namespaces or at the top level) must continue to work as before for backward compatibility.
- A command descriptor field that holds a single specification reference URL should use a singular name rather than a plural name to accurately reflect its content.

## Why This Matters

This makes the devtools configuration API consistent and predictable across all supported browsers, and separates WebdriverIO-internal launch configuration from standard WebDriver capabilities.
