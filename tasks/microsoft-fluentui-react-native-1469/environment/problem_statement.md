## Description

The experimental Checkbox component has a rendering stability issue: every time the component re-renders, it creates new style and accessibility-related objects instead of reusing the previously computed ones. This causes styles to be invalidated on every render pass, which hurts performance and breaks certain React optimization patterns that rely on referential equality of style objects.

Additionally, the component is missing design tokens for:
- Controlling the overall size of the checkbox box element
- Controlling the size of the checkmark indicator inside the box
- Controlling the spacing before the label (when the label appears after the checkbox)

Finally, the component doesn't correctly handle the case where a developer passes additional accessibility actions. The custom actions should be merged with the built-in toggle action, but this is not currently working, and it also causes re-render instability.

## Expected Behavior

- Rendering the component multiple times with the same props should produce the same (referentially equal) style objects — styles must not be recreated on every render.
- The component should re-render identically when props don't change.
- Passing a style object or custom accessibility actions as props should not cause rendering instability.
- A design token for the checkbox box size should be supported, controlling the width and height of the box element.
- A design token for the checkmark size should be supported, controlling the width and height of the checkmark indicator.
- A design token for the label spacing should be supported, adding end-margin spacing to the label to control how far from the checkbox the label appears.
- Custom accessibility actions passed by the developer should be merged with the default toggle action.

## Why This Matters

Rendering instability can trigger infinite re-render loops and hurts performance in production apps. The missing tokens limit developers' ability to customize the Checkbox to match design specs, and the broken accessibility action support prevents developers from adding custom interaction behaviors alongside the default toggle.
