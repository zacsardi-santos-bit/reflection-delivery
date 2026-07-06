## Security Vulnerability: Plugins Load Scripts from Arbitrary URLs

### Description

Several marimo frontend plugins load external JavaScript and CSS resources by inserting script and link elements directly into the page, using URLs that come from widget configuration or HTML attributes. These plugins perform no validation on those URLs before injecting them into the DOM. This means an attacker who can embed a crafted HTML element into a notebook (for example, via a markdown cell) can supply an arbitrary URL — including an absolute address pointing to a remote server, an inline data URI, or a dangerous scheme — and the browser will faithfully load and execute whatever is at that URL.

### Impact

Because the URL is never checked, any value that looks like a script or stylesheet source is accepted: absolute HTTP and HTTPS addresses, protocol-relative links, data URIs containing base64-encoded JavaScript, inline script scheme URIs, local file system paths, and blob URIs. One class of affected plugins also uses the URL content directly as script body text rather than as a source reference, compounding the risk.

### Expected Behavior

- There should be a shared URL validation utility that accepts only internal virtual file paths (paths whose first meaningful segment is the virtual file prefix) and rejects everything else.
- Any URL that is not a recognized virtual file path should be rejected before any DOM element is created, and an error should be logged.
- Virtual file paths that include query strings or fragments should also be rejected, since these can be used to smuggle redirect parameters.
- Non-string inputs should be treated as untrusted.
- The affected plugins — the interactive chart plugin, the widget binding loader, and the panel extension loader — should each be updated to run this check before inserting any element.
- When a script element is inserted for a trusted URL, the URL must be set as the source reference rather than inlined as script body content.

### Why This Matters

Without this fix, a malicious actor only needs to get a notebook to render an HTML element with a crafted URL attribute to execute arbitrary JavaScript in the viewer's browser. Fixing this closes the injection path without breaking legitimate notebook functionality, since all legitimate resources are served through the virtual file system anyway.
