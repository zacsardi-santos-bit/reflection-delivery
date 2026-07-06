## Description

When a web page opens a new window (for example, by clicking a link that opens in a new tab or window), Electron fires an event on the web content component to notify the application. However, this event currently does not include any information about the **referrer** — that is, which page was responsible for opening the new window and what referrer policy applies.

This omission makes it impossible for Electron applications to properly forward referrer context when they intercept and handle new window requests. Web servers that rely on the Referer HTTP header for analytics, access control, or navigation tracking will not receive the expected referrer header when Electron handles the new window event.

## Expected Behavior

- When a new window is requested by clicking a link that targets a new window, the event fired for new window creation should include the referrer context as an additional argument.
- The referrer context should contain:
  - The URL of the originating page
  - The referrer policy that governs how the referrer header is sent
- When the referrer is correctly forwarded, the target URL's server should receive the proper Referer HTTP header.

## Why This Matters

Applications that intercept window creation need to be able to preserve the referrer information so that opened URLs behave as they would in a full browser. Without this, server-side features dependent on the Referer header are broken for windows opened through Electron's new window creation handling.
