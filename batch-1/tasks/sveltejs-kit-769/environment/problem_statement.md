## Description

SvelteKit is vulnerable to cross-site scripting when server-rendered pages include data fetched during the load phase. When data from an API endpoint contains strings that include closing script tags or other HTML script-breaking content, that data is embedded into the rendered HTML without being safely encoded. This allows the injected markup to be interpreted as real HTML rather than plain data, causing unintended scripts to execute in the browser.

## Expected Behavior

- When a page loads data from an endpoint and that data contains characters that could prematurely end an HTML script block, the rendered output should display the data as literal text.
- In server-side rendered pages (before any client-side JavaScript runs), embedded data must not cause any injected script to execute.
- The page should safely present the raw string value — including angle brackets and slash characters — without those characters being treated as HTML markup.

## Why This Matters

Any application that displays user-controlled or third-party API data on a server-rendered page is potentially affected. An attacker who can influence the content of an API response could inject arbitrary JavaScript that runs in every visitor's browser, leading to session hijacking, data theft, or other malicious outcomes. Proper escaping of inline data is a fundamental requirement for safe server-side rendering.
