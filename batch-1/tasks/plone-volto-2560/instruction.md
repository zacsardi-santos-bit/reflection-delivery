Implement a reusable React component named `BlockChooserButton` that encapsulates the logic for displaying an "add block" button in Volto CMS. This component should conditionally render a button based on the block's data and allow for a custom button component to be used.

*   Create the `BlockChooserButton` component as the default export in `src/components/manage/BlockChooser/BlockChooserButton.jsx`.
*   Ensure the component signature is `BlockChooserButton({ data, block, buttonComponent, allowedBlocks, showRestricted, onMutateBlock, onInsertBlock, blocksConfig, ...rest })`.
*   Render nothing if the block's data object lacks an '@type' field or if the block type is unrecognized.
*   Use the `blockHasValue` function from `@plone/volto/helpers` to determine if a block is empty.
    *   Treat blocks with unrecognized or missing '@type' as having a value (no button shown).
*   When the block is empty and recognized, render a default button using Semantic UI React's `Button` component:
    *   Include the 'icon' and 'basic' props, and use the className 'block-add-button'.
    *   Ensure the button has CSS classes 'ui basic icon button block-add-button' and a title attribute 'Add block'.
    *   Include an SVG icon using Volto's `Icon` component with the 'circle-plus' SVG, className 'block-add-button', and size '19px'.
    *   The SVG must have class 'icon block-add-button' and inline style 'height: 19px; width: auto; fill: currentColor;'.
*   Accept an optional `buttonComponent` prop to replace the default button when the block is empty.
    *   Forward all original props to the custom component if provided.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.