I'm working on a project that uses a development server with hot module replacement. When I introduce a syntax error in one of my source files, the dev server crashes instead of staying alive and reporting the error to the browser. I'd expect to see a clear message in the browser console telling me which file failed to parse, so I know what to fix.

Even more frustrating: once I fix the error and save the file, the HMR system doesn't recover properly — it doesn't re-apply the update, so I have to restart the whole dev server. The expected behavior is that fixing the error triggers a successful hot update and the page reflects the corrected code immediately.

Can you fix the HMR system so that: (1) syntax errors are reported to the browser console with a clear, prefixed message identifying the failing module, and (2) the server stays alive so that fixing the error and saving triggers a proper recovery and hot update?
