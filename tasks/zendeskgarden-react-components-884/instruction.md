Implement a new `DrawerModal` component in the modals package that slides in from the side of the screen. Ensure it supports a header, body, footer, and close button as compound components. The drawer should handle closing via the ESC key, backdrop clicks, and the close button, while locking page scroll when open. Provide proper accessibility support, ref forwarding, and RTL layout support.

*   Export the `DrawerModal` component from the modals package.
*   Implement the `DrawerModal` component with the following features:
    *   Accept an `isOpen` prop to control visibility.
    *   Accept an `onClose` callback prop for closing actions.
    *   Render a dialog with `role='dialog'` when `isOpen` is true.
    *   Do not render to the DOM when `isOpen` is false.
    *   Lock page scrolling by setting body overflow to 'hidden' when open.
    *   Restore previous body overflow when closed.
    *   Close the drawer when the ESC key is pressed.
    *   Close the drawer when the backdrop is clicked.
    *   Close the drawer when the Close button is clicked.
    *   Ensure the Close button has an accessible label 'Close modal'.
    *   Accept an `id` prop to apply accessibility attributes to descendants.
    *   Accept `backdropProps` to spread additional props onto the backdrop.
    *   Forward refs to the underlying dialog DOM element.
*   Implement the following compound components with ref forwarding:
    *   `DrawerModal.Header` - Forward refs to the underlying DOM element.
    *   `DrawerModal.Body` - Forward refs to the underlying DOM element.
    *   `DrawerModal.Close` - Forward refs to the underlying button element.
*   Ensure the DrawerModal supports RTL layouts by sliding in from the left side when applicable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.