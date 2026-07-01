Implement a new `gap` property for the Box component to control spacing between child elements using predefined size tokens. Ensure that this property is documented and integrated into the component's existing structure and documentation.

*   Add a `gap` prop to the Box component in `src/js/components/Box/Box.js`.
    *   Accept values: 'xsmall', 'small', 'medium', 'large', 'xlarge'.
    *   Insert spacing elements between adjacent non-null children based on the `gap` value.
    *   Do not insert spacing before the first child or after the last child.
    *   Ensure no spacing is added if there is only one child.

*   Forward the `direction` prop explicitly in the Box component.
    *   Ensure `direction` appears before other dynamically spread props like `id`, `overflow`, and `aria-hidden` in the DOM.

*   Update the Box component's documentation in `src/js/components/Box/doc.js`.
    *   Define the `gap` prop with PropTypes: `PropTypes.oneOf(['xsmall', 'small', 'medium', 'large', 'xlarge'])`.
    *   Add the description: 'The amount of spacing between child elements.'

*   Modify the Box component's README in `src/js/components/Box/README.md`.
    *   Add a "gap" section with the description: 'The amount of spacing between child elements.'
    *   List valid values: xsmall, small, medium, large, xlarge.
    *   Place this section between the "full" and "gridArea" sections.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.