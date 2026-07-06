## Description

Webpack's HTML experiment should make HTML files a true first-class module type, but right now the feature falls short in several ways. The CLI documentation for the flag explicitly warns that HTML files cannot be used directly as entry points without additional handling — yet the whole point of the experiment is to support exactly that. Similarly, when a developer enables the HTML or CSS experiment and uses a bare directory path as their entry point, webpack does not automatically discover HTML or CSS index files the way it discovers JavaScript index files. A directory entry finds a JavaScript index file by default, but an HTML or CSS index file is never found even when the relevant experiments are on.

## Expected Behavior

- When the HTML experiment is enabled, webpack's resolve configuration should automatically include the HTML extension in its lookup lists, so an extensionless entry resolves to an HTML index file. When both an HTML and a JavaScript index file exist in the same directory, the HTML file should take priority over the JavaScript file.
- When the CSS experiment is enabled, webpack's resolve configuration should automatically include the CSS extension in its lookup lists, so an extensionless entry can resolve to a CSS index file.
- When an HTML file is used as an entry point containing references to scripts and stylesheets, webpack should process those assets through its normal bundling pipeline and rewrite the emitted HTML to reference the output chunk files rather than the original source paths.
- The CLI description for the HTML experiment flag should be updated to reflect that HTML files are now a first-class module type usable directly as entry points.

## Why This Matters

Developers who enable the HTML experiment expect to be able to drop an HTML index file into their source directory and have webpack pick it up automatically — just like a JavaScript index file works today. The current behavior (and the misleading documentation) undermines the purpose of the experiment. Making HTML and CSS genuinely discoverable via the standard resolution mechanism, and ensuring HTML entry processing correctly bundles referenced assets, completes the feature as users expect it to work.
