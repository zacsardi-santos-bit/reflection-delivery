Implement dynamic font sizing for the FormLabel component based on its position within the FormControl. Ensure that custom typography settings provided by developers override any automatic sizing applied due to label position.

*   Adjust FormLabel font size based on position:
    *   When FormControl is rendered with the default label position (top), set FormLabel font-size to 1.3rem.
    *   When FormControl is rendered with labelPosition set to 'left', set FormLabel font-size to 1.4rem.
*   Ensure custom typography settings override position-based sizing:
    *   If FormLabel is explicitly provided with typography props specifying a font size, such as 1.8rem, this size must take precedence over the automatic position-based font size.
*   Add a new component for testing:
    *   Create a named export `SingleFieldFormWithLabelFont` in `src/components/Forms/FormControl/__mocks__/forms.tsx`.
    *   This component must be a single-field form where FormLabel uses custom typography props, resulting in a rendered font-size of 1.8rem on the label node.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.