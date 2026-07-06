## Description

Stylelint currently has no rule to catch deprecated CSS selectors. Developers may unknowingly write stylesheets using element names, pseudo-classes, or pseudo-elements that have been deprecated or removed from web specifications and may no longer work in modern browsers. These include obsolete HTML element names (such as those removed from the HTML living standard), outdated pseudo-class syntax that has been superseded by modern equivalents, and old pseudo-elements that have been replaced by newer versions.

## Expected Behavior

A new lint rule should:

- Flag deprecated HTML element type selectors (case-insensitively)
- Flag deprecated SVG element type selectors (case-sensitively, as SVG element names are case-sensitive)
- Flag deprecated pseudo-classes (case-insensitively), distinguishing between those that have a modern replacement and those that do not
- Flag deprecated pseudo-elements (case-insensitively), distinguishing between those that have a modern replacement and those that do not
- Auto-fix deprecated selectors that have a known modern replacement by substituting the deprecated form with its replacement
- Report precise source positions for each deprecated selector found
- When multiple deprecated selectors appear in the same rule, report each one separately
- Support a secondary option for ignoring specific selectors, accepting both plain string names and regular expression patterns, allowing specific deprecated selectors to be excluded from reporting

## Why This Matters

This fills a gap in the existing set of "no-deprecated" rules for declarations, media types, and properties. Catching deprecated selector usage helps teams modernize their CSS and avoid relying on browser behavior that may already be removed or on its way out.
