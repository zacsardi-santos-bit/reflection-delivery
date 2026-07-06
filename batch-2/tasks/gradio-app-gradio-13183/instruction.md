I'm working with the Accordion component in Gradio and running into a couple of issues.

*   The Accordion component must accept a 'label' string prop and render it as the accessible name of a toggle button. The button must contain a first <span> element whose trimmed text content matches the label value; an empty string label must result in an empty first span.

*   The Accordion component must accept an 'open' boolean prop. The content area must have a data-testid attribute of 'accordion-content'. When open=true, that element's display style must not be 'none'. When open=false, that element's display style must be 'none'.

*   Clicking the toggle button must toggle content visibility: open becomes closed and closed becomes open. Clicking twice must restore the original state.

*   When the user clicks to open a closed accordion, the component must dispatch both an 'expand' event and a 'gradio_expand' event exactly once each. When the user clicks to close an open accordion, only a 'collapse' event must be dispatched exactly once.

*   No 'expand', 'collapse', or 'gradio_expand' events must be dispatched on initial mount, regardless of the initial 'open' value.

*   On repeated user-driven toggles, events must alternate correctly: each time the accordion opens an 'expand' fires, and each time it closes a 'collapse' fires.

*   The component's set_data method must dispatch events only when the 'open' state actually changes. When transitioning closed-to-open, dispatch 'expand' followed by 'gradio_expand'. When transitioning open-to-closed, dispatch only 'collapse'. When set_data is called with the same open value that is already set, dispatch no events and make no DOM changes.

*   Calling set_data with a new label value must update the button's accessible label text to the new value.

*   The component's get_data method must return an object containing an 'open' property that reflects the current open state. After set_data changes the open value, get_data must return the updated value.

*   When the 'visible' prop is false (boolean), the component must remain in the DOM (its element must not be null) but must not be visible. This is the same behavior as visible='hidden'.

*   The component must accept 'elem_id' and 'elem_classes' props applied to the wrapper element.

*   The run_shared_prop_tests utility must support a 'visible_false_hides' boolean option (defaulting to false). When visible_false_hides is true, the shared visible=false test must verify the element stays in the DOM but is not visible. When visible_false_hides is false (default), the test must verify the element is removed from the DOM.


*   Interface details: Type: Component
Name: Accordion
Location: js/accordion/Index.svelte
Description: A collapsible accordion Svelte component. Accepts `label` (string), `open` (boolean), `elem_id` (string), `elem_classes` (string[]), and `visible` (boolean | 'hidden') props. Dispatches `expand`, `collapse`, and `gradio_expand` events. The content area must have the attribute data-testid="accordion-content" and use display style (none vs not-none) to show/hide. The toggle button must contain a first <span> element holding the label text. Must implement get_data and set_data behavior for programmatic access (see below).

Content element requirement:
- The collapsible content wrapper div in js/accordion/shared/Accordion.svelte must have attribute: data-testid="accordion-content"
- It controls visibility via style:display — "none" when closed, non-"none" when open

Type: Function
Name: set_data
Location: js/accordion/Index.svelte (the AccordionGradio class's set_data method)
Signature: set_data(data: Partial<object>) -> void
Description: Programmatically updates the accordion's state. Must only dispatch events when the open state actually changes (not when the new value equals the current value):
- When data.open is true AND current open is false: dispatch "expand" then dispatch "gradio_expand"
- When data.open is false AND current open is true: dispatch "collapse"
- When data.open equals current open state: dispatch no events, make no DOM changes
- When data.label is provided: update the button's displayed label text
After dispatching events, must call super.set_data(data) to apply the change.

Type: Function
Name: get_data
Location: js/accordion/Index.svelte (via the Gradio base class)
Signature: get_data() -> { open: boolean, ...otherProps }
Description: Returns the current state of the accordion. The returned object must include an `open` property reflecting whether the accordion is currently open. After set_data changes the open value, get_data must reflect the updated state.

Type: Interface
Name: SharedPropTestConfig (visible_false_hides field)
Location: js/tootils/src/shared-prop-tests.ts
Description: The SharedPropTestConfig interface must include an optional `visible_false_hides?: boolean` field (defaulting to false). When this option is true, run_shared_prop_tests must run a test that verifies visible=false keeps the element in the DOM but not visible (rather than removing it). When false (the default), run_shared_prop_tests must run the test that verifies visible=false removes the element from the DOM.

Events dispatched by Accordion:
- "expand": fired when accordion transitions from closed to open (user click or set_data)
- "collapse": fired when accordion transitions from open to closed (user click or set_data)
- "gradio_expand": fired when accordion transitions from closed to open (user click or set_data); NOT fired when closing
- No events are dispatched on initial component mount

DOM structure requirements:
- Content container must have attribute: data-testid="accordion-content" (in js/accordion/shared/Accordion.svelte)
- Content is shown when display style is not "none"; hidden when display style is "none"
- Toggle button must have role="button" with accessible name matching label prop
- Toggle button must contain a first <span> element whose textContent matches the label prop value

Visibility behavior:
- When visible=false, the component must remain in the DOM but be visually hidden (not visible); the element with elem_id must not be null in the DOM
- When visible='hidden', same behavior as visible=false: stay in DOM, not visible
- When visible=true, render the component normally


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.