## Description

ScrapeGraphAI currently only supports a Chromium-based document loader for fetching web page content. Chromium requires significant memory (~300MB per session), depends on a full browser installation, and produces verbose output with lots of boilerplate — increasing token costs when feeding content to language models.

There is a need for a lighter-weight document loader that can fetch and extract web page content without requiring a full browser runtime. Such a loader should integrate naturally into the existing loader pattern: accept a list of URLs, fetch each page, and return structured document objects with source metadata.

## Expected Behavior

- The loader accepts a list of URLs plus optional configuration: output format, timeout, a CSS/ARIA selector to scope extraction, and custom HTTP headers.
- It supports at least three output formats: plain text, markdown, and a structured object model format.
- It validates the output format at construction time and rejects unsupported values with a clear error.
- On success, it yields one document per URL, with the page content and metadata identifying the source URL, the loader name, and the format used.
- Empty responses are silently skipped with a warning log.
- Timeouts are handled gracefully — the URL is skipped and a warning is logged.
- If the underlying tool is not installed, a clear installation error is raised immediately.
- Both synchronous and asynchronous loading modes are supported.
- Empty URL lists produce no output without errors.

## Why This Matters

Users running AI-powered scraping pipelines benefit from faster, lower-memory page fetching that produces cleaner, more compact content — reducing LLM costs and improving reliability for server-rendered pages.
