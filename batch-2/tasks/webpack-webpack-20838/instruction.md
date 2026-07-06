I'm running into a bug with webpack's CSS module handling when mixing export types.

*   When a CSS file configured as 'text' export type is imported via a CSS-level @import statement by another CSS file configured as 'style' export type, the imported file's content must be injected into the DOM as a style tag (not merely exported as a string), because the parent module's export type must take precedence.

*   When a CSS file configured as 'style' export type uses @import to pull in a CSS file configured as 'text' export type, a style tag containing the imported file's CSS rules must be present in the DOM, making those rules active on the page.

*   A CSS file configured as 'text' export type that is directly imported in JavaScript (not via @import by a style-type parent) must still export its CSS content as a string value.

*   A CSS file configured as 'style' export type using CSS Modules must still correctly export scoped class name strings for its own classes.

*   A style tag for the parent 'style' export type CSS file's own rules must be present in the DOM, independently of any @imported children.

*   In the bundled output, CSS module comment annotations must correctly reflect the effective export type used (i.e., 'style' rather than 'text') when a text-type file is @imported by a style-type parent.


*   Interface details: NO INTERFACES NEEDED

The fix is an internal change to webpack's CSS module processing logic. The tests validate observable behavior (DOM style tag injection, string exports, class name exports, and snapshot output format) without importing or calling any new named functions or classes. The failing tests exercise existing webpack configuration APIs (`css/module` type, `exportType` parser option) which are already part of the codebase. No new public symbols need to be introduced.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.