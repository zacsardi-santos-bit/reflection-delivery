I'm working with the experimental Checkbox component in the Fluent UI React Native library and I've run into a few issues I need help fixing.

First, the component has a rendering stability problem. Each time the component re-renders — even with identical props — it's producing brand new style and accessibility objects rather than reusing the ones from the previous render. This breaks render consistency checks and is a performance concern because downstream components relying on object identity for change detection will always see a "change." When I render the component twice with the same props (with or without a style override), I expect to get the same computed style objects both times.

Second, when I pass an additional accessibility action via props, the component doesn't handle it correctly. Instead of merging the custom action with the default toggle action, the behavior breaks down and render consistency fails. The default toggle action should always be present, and any additional actions should be appended alongside it.

Third, the component is missing some design tokens I need: one to control the overall size of the checkbox box (width and height of the box element), one to control the size of the checkmark indicator inside the box, and one to control the amount of spacing between the checkbox and the label when the label appears after the checkbox.

These issues collectively make the component harder to use reliably in production, both from a performance and a customization standpoint. I'd like all three to be addressed: fix the rendering stability, fix the accessibility actions merging, and add the missing size and spacing tokens.
