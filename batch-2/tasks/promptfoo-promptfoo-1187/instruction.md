Implement the InfoModal component to ensure all links open in a new browser tab and the dialog behaves correctly in all respects. Ensure the component is accessible and adheres to the specified interface and requirements.

*   Implement the InfoModal component in `src/web/nextui/src/app/components/InfoModal.tsx`.
    *   Accept two props: `open` (boolean) and `onClose` (callback function).
*   Visibility and Content:
    *   When `open` is false, ensure no visible content is rendered, including the title 'About Promptfoo'.
    *   When `open` is true, display:
        *   Title: 'About Promptfoo'.
        *   Version: Read from PROMPTFOO_VERSION environment variable, displayed as 'Version {value}'.
        *   Description: 'Promptfoo is a MIT licensed open-source tool'.
        *   Links with exact labels and destinations:
            *   'Documentation' → 'https://www.promptfoo.dev/docs/intro'
            *   'GitHub Repository' → 'https://github.com/promptfoo/promptfoo'
            *   'File an Issue' → 'https://github.com/promptfoo/promptfoo/issues'
            *   'Join Our Discord Community' → 'https://discord.gg/gHPS9jjfbs'
            *   'Book a Meeting' → 'https://cal.com/team/promptfoo/intro'
        *   Ensure all links have `target='_blank'`.
*   Interaction:
    *   Render a 'Close' button that invokes the `onClose` callback exactly once when clicked.
*   Accessibility:
    *   Ensure the dialog element has an `aria-labelledby` attribute with the value 'about-promptfoo-dialog-title'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.