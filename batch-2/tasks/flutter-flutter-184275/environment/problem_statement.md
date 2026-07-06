## Description

When building a Flutter web application for release with CDN-hosted assets disabled, the build system does not automatically include a local fallback Roboto font. This means apps that rely on Roboto for text rendering may display text incorrectly or fall back to system fonts in environments where CDN resources are unavailable — such as offline environments, corporate networks with CDN restrictions, or air-gapped deployments.

## Expected Behavior

- When a release web build is configured to use locally-bundled assets instead of CDN assets, the build should automatically bundle a local Roboto font as a fallback.
- The bundled font file should appear at a predictable location in the output assets.
- The font should be registered in the font manifest with the correct family name and asset path so the Flutter runtime can discover and load it.
- The bundling should happen automatically without any extra developer configuration.

## Why This Matters

Developers opting into fully self-contained web deployments (no CDN dependencies) currently have no built-in way to ensure Roboto is available. They may notice missing or wrong fonts only after deploying, which is a poor experience. This change makes the local-assets build mode produce a complete, self-contained artifact with proper font fallback support automatically.
