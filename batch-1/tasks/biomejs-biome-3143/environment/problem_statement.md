## Description

The autocomplete attribute on form input elements only accepts a specific set of standardized values as defined by the HTML autofill specification and related accessibility guidelines (WCAG 1.3.5). When developers accidentally use invalid, misspelled, or improperly combined tokens, browsers silently ignore them — breaking autofill functionality and potentially causing accessibility audit failures. Currently there is no lint rule in the nursery category to catch these mistakes.

## Expected Behavior

A new lint rule should be added to the nursery category that:

- Validates the autocomplete attribute on native HTML input elements
- Reports a diagnostic whenever an invalid, unknown, or improperly ordered set of autocomplete tokens is used
- Accepts a configuration option that is an array of custom component names, so that user-defined input wrapper components are also validated
- Only checks components listed in that configuration option; unlisted custom components are ignored
- Skips validation when the attribute value is a dynamic expression rather than a static string
- Skips validation when the element's input type is determined dynamically
- Does not report for missing, empty, or boolean-form autocomplete attributes

## Diagnostic Messages

When an invalid value is detected, the diagnostic should state that valid values for the autocomplete attribute must be used, with an additional note explaining that the attribute accepts only a fixed set of specific values, and links to WCAG 1.3.5, the HTML Living Standard autofill section, and the MDN reference for the autocomplete attribute.

## Why This Matters

Catching these mistakes at lint time prevents silent accessibility and autofill failures that would otherwise only surface at runtime (or not at all, since browsers silently ignore unknown values). It also helps teams comply with WCAG 1.3.5 without relying on manual review.
