## Description

The browser automation feature currently launches without asking users whether they consent to the data collection and privacy implications involved. Users have no opportunity to review what the browser agent does with their data before it starts. Additionally, users who have already opted out of usage statistics in their global preferences have that choice ignored when the browser agent runs — the underlying browser process still collects telemetry regardless of their setting.

## Expected Behavior

- Before launching the browser automation component for the first time, users should be presented with a privacy notice that explains what data may be collected and how it is handled, including a reference to the applicable privacy policy. Users must actively accept this notice before the browser agent proceeds.
- Once a user has accepted the privacy notice, it should not be shown again on subsequent runs.
- In non-interactive (scripted or headless) environments where no user interface is available, the notice should be automatically accepted so automated workflows are not blocked.
- If a user declines the privacy notice, the browser agent should not proceed.
- When the user has opted out of usage statistics in their configuration, the browser process must be launched with the appropriate flags to disable telemetry and performance data collection, so the user's privacy preference is consistently enforced.

## Why This Matters

Users running the browser agent deserve transparency about data collection practices and should have a clear point of control. Furthermore, existing privacy preferences (like opting out of usage statistics) should be respected uniformly across all components, including browser automation.
