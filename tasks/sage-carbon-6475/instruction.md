Implement improvements to the Card component family to enhance alignment with the design system and developer ergonomics. Ensure automatic sharing of spacing values, simplify interactive behavior, and support data tagging for better test automation.

*   Update Card component:
    *   Accept `onClick` and `href` props for interactivity.
        *   Render `StyledCardContent` as a `<button>` when `onClick` is provided.
        *   Apply pointer cursor and box-shadow on hover/focus for both `onClick` and `href`.
    *   Accept a `width` prop to set CSS width on `StyledCard`.
    *   Issue a console warning if `CardFooter` is a direct child with `onClick` or `href`.
    *   Propagate spacing via `CardContext` for sub-components.

*   Modify Card sub-components:
    *   CardColumn:
        *   Accept `data-element` and `data-role` props.
        *   Default `data-component` to "card-column" and `data-element` to "card-column".
    *   CardFooter:
        *   Accept `data-element` and `data-role` props.
        *   Default `data-component` to "card-footer" and `data-element` to "card-footer".
    *   CardRow:
        *   Read spacing from `CardContext` instead of a direct prop.
        *   Use `&&` CSS specificity modifier for padding.
        *   Accept `data-element` and `data-role` props.
        *   Default `data-component` to "card-row" and `data-element` to "card-row".

*   Update `StyledCardContent` in `card.style.tsx`:
    *   Export as a named styled component.
    *   Apply padding and margin based on `spacing` from `CardContext`.
    *   Adjust border radii based on the presence of a footer.

*   Adjust `CardContextProps` in `card-context/index.ts`:
    *   Include a `spacing` property typed as "small" | "medium" | "large".

*   Export `marginSizes` and `paddingSizes` in `card.style.tsx`:
    *   Map spacing values to CSS strings for margin and padding.

*   Relocate Polish locale module:
    *   Move from `src/locales/pl-pl` to `src/locales/__internal__/pl-pl`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.