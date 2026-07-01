I'm working on improving the accessibility of our dropdown select components. Right now, the trigger button on our single-select and multi-select dropdown components uses a generic role that doesn't properly communicate to screen readers that these are selection widgets. I need the trigger to identify itself as a combo box control.

On top of that, there's currently no way to provide a stable accessible label for these components. The control's accessible name always reflects the currently selected option, so once a user picks something, the label changes and screen readers no longer announce the field's purpose. I'd like both the single-select and multi-select components to accept an accessible label prop that persists as the control's accessible name regardless of the current selection state.

This should also work with custom openers — if the component has an accessible label and a custom opener is used, the label should be passed down to the custom opener. But if the custom opener defines its own accessible label, that one should take priority.

Finally, the exported TypeScript types for the labels configuration on both components should be renamed to better reflect what they represent.
