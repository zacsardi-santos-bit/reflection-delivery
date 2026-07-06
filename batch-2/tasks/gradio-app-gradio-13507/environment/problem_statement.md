## Description

The HTML component silently ignores script tags embedded in its content, which confuses users who expect those scripts to execute. Because of the way the HTML component injects content into the page, browsers do not run scripts included this way — yet no feedback is given to the developer when this happens.

## Expected Behavior

- When a developer creates an HTML component with content that includes a script tag, a warning should be raised informing them that the script will not execute.
- When a developer creates an HTML component with regular HTML content (no script tags), no warning should be raised.

## Why This Matters

Developers often try to load external libraries or run inline scripts by embedding script tags inside the HTML component's content, only to find the scripts silently do nothing. This change provides an early warning that lets developers know they need to use an alternative mechanism to run scripts alongside their HTML content, saving time spent debugging a non-obvious platform limitation.
