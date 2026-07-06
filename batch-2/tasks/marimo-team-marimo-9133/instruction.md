I'm working on a security fix for the marimo frontend.

*   The isTrustedVirtualFileUrl function must return true for URLs of the form '@file/<filename>', './@file/<filename>', or '/@file/<filename>', where <filename> may contain dots (e.g. 'name.with.dots.js').

*   The isTrustedVirtualFileUrl function must return false for non-string inputs including null, undefined, numbers, and objects.

*   The isTrustedVirtualFileUrl function must return false for: empty string, absolute HTTP/HTTPS URLs, protocol-relative URLs (starting with '//'), and URLs using dangerous schemes (javascript:, data:, file:, blob:).

*   The isTrustedVirtualFileUrl function must return false for near-miss patterns: '../@file/x.js' (parent traversal), './malicious/@file/x.js' (extra path segment before @file), '@file' alone without a filename, '@files/x.js' (wrong prefix), and './evil.js' (no @file segment at all).

*   The isTrustedVirtualFileUrl function must return false for URLs that are otherwise valid virtual file paths but contain a query string (e.g., './@file/x.js?redirect=http://evil.com') or a fragment (e.g., './@file/x.js#http://evil.com').

*   The WidgetDefRegistry.getModule method must reject with an error whose message matches the pattern /untrusted/i (case-insensitive) when given an untrusted URL such as absolute HTTP/HTTPS URLs, protocol-relative URLs, javascript: or data: scheme URLs, empty strings, or virtual file paths with query strings.

*   The WidgetDefRegistry.getModule method must NOT reject with an 'untrusted' error when given a valid virtual file URL such as './@file/123-widget.js'; the import may fail for other reasons (e.g., test environment) but not because the URL was rejected as untrusted.

*   The ensureMplJs function (exported via visibleForTesting) must resolve immediately without appending any DOM element when window.mpl is already set, regardless of the URL provided.

*   The ensureMplJs function must reject with an error matching /untrusted/i and must NOT call document.head.append for untrusted URLs including absolute HTTP/HTTPS addresses, protocol-relative URLs, javascript: scheme URLs, data: URIs, and virtual file paths with query strings.

*   The ensureMplJs function must append a single HTMLScriptElement to document.head (with src containing the virtual file path) for trusted virtual file URLs, and resolve when the script load event fires.

*   The injectCss function (exported via visibleForTesting) must NOT append any <link> element to the container for untrusted URLs; it must log an error containing 'untrusted' via Logger.error; and it must return a cleanup function that can be called without throwing.

*   The injectCss function must append a <link> element with rel='stylesheet' and the exact href string equal to the trusted virtual file URL to the container; the returned cleanup function must remove that link element from the container.

*   The resetMplJsLoading function (exported via visibleForTesting) must reset the module-level script-loading state so that subsequent calls to ensureMplJs behave as if no script has been loaded yet.

*   The loadPanelExtension function must return false and produce no side effects when called with null.

*   The loadPanelExtension function must return false, not append any script element, and log an error containing 'untrusted' via Logger.error when given untrusted URLs (absolute HTTP/HTTPS, protocol-relative, javascript:, data:, or virtual file paths with fragments).

*   The loadPanelExtension function must return true and append an HTMLScriptElement to document.head with src containing the virtual file path and with empty innerHTML when given a trusted virtual file URL.


*   Interface details: Type: Function
Name: isTrustedVirtualFileUrl
Location: frontend/src/plugins/core/trusted-url.ts
Signature: isTrustedVirtualFileUrl(url: unknown): boolean
Description: Validates whether a URL is a trusted virtual file path. Returns true only for URLs of the form `@file/<filename>`, `./@file/<filename>`, or `/@file/<filename>` (with no query string, fragment, parent-directory traversal, or extra path segments before @file). Returns false for all other inputs including non-strings, empty string, absolute HTTP/HTTPS URLs, protocol-relative URLs, dangerous schemes (javascript:, data:, file:, blob:), and near-miss patterns like `../@file/`, `./x/@file/`, `@file` alone, or `@files/`.

Type: Object
Name: visibleForTesting
Location: frontend/src/plugins/impl/mpl-interactive/MplInteractivePlugin.tsx (or .ts)
Description: A named export object that exposes internal functions for testing. Must contain the properties: ensureMplJs, injectCss, and resetMplJsLoading.

Type: Function
Name: ensureMplJs
Location: frontend/src/plugins/impl/mpl-interactive/MplInteractivePlugin.tsx (or .ts)
Signature: ensureMplJs(url: string): Promise<void>
Description: Ensures the MPL JavaScript is loaded. If window.mpl is already set, resolves immediately without any DOM side effects (even for malicious URLs). For untrusted URLs (as determined by isTrustedVirtualFileUrl), rejects with an error whose message matches /untrusted/i and does NOT append any element to document.head. For trusted virtual file URLs, appends an HTMLScriptElement with src set to the URL to document.head and resolves when the script's load event fires.

Type: Function
Name: injectCss
Location: frontend/src/plugins/impl/mpl-interactive/MplInteractivePlugin.tsx (or .ts)
Signature: injectCss(container: HTMLElement, url: string): () => void
Description: Injects a CSS stylesheet link into the given container. For untrusted URLs, does NOT append any <link> element, calls Logger.error with a message containing "untrusted", and returns a no-op cleanup function that does not throw. For trusted virtual file URLs, appends a <link rel="stylesheet"> element with the given href to the container, and returns a cleanup function that removes that element.

Type: Function
Name: resetMplJsLoading
Location: frontend/src/plugins/impl/mpl-interactive/MplInteractivePlugin.tsx (or .ts)
Signature: resetMplJsLoading(): void
Description: Resets module-level state tracking whether the MPL JS script has been loaded. Used to allow test isolation between test runs.

Type: Function
Name: loadPanelExtension
Location: frontend/src/plugins/impl/panel/PanelPlugin.tsx (or .ts)
Signature: loadPanelExtension(url: string | null): boolean
Description: Loads a Panel extension script. Returns false for null input without any side effects. For untrusted URLs, returns false, does not append any script element to document.head, and calls Logger.error with a message containing "untrusted". For trusted virtual file URLs, appends an HTMLScriptElement with src set to the URL and an empty innerHTML to document.head, and returns true. The script must NOT set innerHTML (that was the original vulnerability sink).

Type: Class
Name: WidgetDefRegistry
Location: frontend/src/plugins/impl/anywidget/widget-binding.ts
Description: Registry class that manages widget module definitions. Must be updated so that its getModule method validates URLs using isTrustedVirtualFileUrl before attempting to load modules.

Type: Method
Name: WidgetDefRegistry.getModule
Location: frontend/src/plugins/impl/anywidget/widget-binding.ts
Signature: getModule(jsUrl: string, jsHash: string): Promise<unknown>
Description: Loads a widget module by URL and hash. Must call isTrustedVirtualFileUrl to validate the URL before loading. For untrusted URLs (absolute HTTP/HTTPS, protocol-relative, dangerous schemes, virtual file paths with query strings, or empty strings), must reject with an error whose message matches /untrusted/i (case-insensitive). For trusted virtual file URLs, may still reject for other reasons (e.g., import not available in the test environment) but must NOT reject with an "untrusted" error.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.