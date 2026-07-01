Implement a new lint rule to flag static HTML elements with interactive event handlers in JSX code. Ensure the rule identifies elements without inherent interactive roles and suggests adding an appropriate role for accessibility.

*   Define the lint rule as 'noStaticElementInteractions' in the 'nursery' category.
    *   Produce diagnostics under 'lint/nursery/noStaticElementInteractions'.
    *   Emit a diagnostic message: 'Static Elements should not be interactive.'
    *   Include a note: 'To add interactivity such as a mouse or key event listener to a static element, give the element an appropriate role value.'

*   Flag static HTML elements with interactive event handlers:
    *   Interactive handlers include: onClick, onKeyDown, onKeyPress, onKeyUp, onMouseDown, onMouseUp.
    *   Static elements include: div, span, a (without href), area, b, base, bdi, bdo, body, cite, col, colgroup, data, head, header, hgroup, i, kbd, link, map, meta, noscript, object, picture, q, rp, rt, s, samp, script, section (without accessibility label), small, source, style, title, track, u, var, wbr.
    *   Elements with role={undefined} or role='presentation' must be flagged.
    *   Elements with abstract ARIA roles (command, composite, input, landmark, range, roletype, sectionhead, select, structure, widget, window) must be flagged.
    *   Elements with aria-hidden={false} must still be flagged if they are static elements with interactive handlers.
    *   Elements with spread props (e.g., {...props}) must be flagged.

*   Do not flag:
    *   Custom components (PascalCase names like TestComponent, Button).
    *   Elements without interactive event handlers or with handlers set to null.
    *   Elements with aria-hidden or aria-hidden={true}.
    *   Natively interactive HTML elements: input (all types), button, datalist, option, select, textarea, audio, form, and anchor elements with an href attribute.
    *   Elements with explicit interactive ARIA roles: button, checkbox, combobox, gridcell, link, menuitem, menuitemcheckbox, menuitemradio, option, radio, searchbox, slider, spinbutton, switch, tab, textbox.
    *   Non-interactive event handlers: onCopy, onCut, onPaste, onCompositionEnd, onCompositionStart, onCompositionUpdate, onChange, onInput, onSubmit, onSelect, onTouchCancel, onTouchEnd, onTouchMove, onTouchStart, onScroll, onWheel, onAbort, onCanPlay, onCanPlayThrough, onDurationChange, onEmptied, onEncrypted, onEnded, onError, onLoadedData, onLoadedMetadata, onLoadStart, onPause, onPlay, onPlaying, onProgress, onRateChange, onSeeked, onSeeking, onStalled, onSuspend, onTimeUpdate, onVolumeChange, onWaiting, onLoad, onAnimationStart, onAnimationEnd, onAnimationIteration, onTransitionEnd.
    *   HTML elements with inherent non-interactive ARIA roles: address, article, aside, blockquote, br, canvas, caption, code, details, dd, del, dfn, dl, dt, em, embed, fieldset, figcaption, figure, h1-h6, hr, html, iframe, img, ins, label, legend, li, main, mark, menu, meter, nav, ol, optgroup, output, p, pre, progress, ruby, strong, sub, sup, table, tbody, tfoot, td, th, thead, time, tr, video, ul.
    *   A section element with an accessibility label (aria-label or aria-labelledby attribute).

*   Implement the `NoStaticElementInteractions` struct in `crates/biome_js_analyze/src/lint/nursery/no_static_element_interactions.rs`:
    *   Implement the biome `Rule` trait.
    *   The `diagnostic` method must return a `RuleDiagnostic` with the specified message and note.

*   Declare the module `pub mod no_static_element_interactions;` in `crates/biome_js_analyze/src/lint/nursery.rs`.
    *   Include `NoStaticElementInteractions` in the `declare_lint_group!` macro.

*   Implement the `is_not_static_element` method in `crates/biome_aria/src/roles.rs`:
    *   Signature: `is_not_static_element(&self, element_name: &str, attributes: &FxHashMap<String, Vec<String>>) -> bool`.
    *   Return true for elements with non-static semantics, false for static elements.

*   Register the diagnostic category 'lint/nursery/noStaticElementInteractions' in `crates/biome_diagnostics_categories/src/categories.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.