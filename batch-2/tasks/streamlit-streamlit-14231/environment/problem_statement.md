## Description

The components and utility functions for displaying uploaded file chips in the chat input are currently implemented as private, chat-input-specific code. As we expand file upload support to other widgets, these components need to be available as shared, reusable building blocks rather than living inside a single widget's directory.

This issue tracks refactoring the uploaded file chip display component — along with related utility functions for file type icons, filename truncation, and image previews — into the shared component library. The visual behavior and accessibility attributes should remain the same, but the components should live in a shared location accessible to any widget.

## Expected Behavior

- The file chip display component should be renamed to reflect its generic, shared nature and moved to the shared component library.
- All related utility functions (file type icon lookup, file extension parsing, image file detection, filename truncation, and image preview URL management) should be consolidated into a single shared utilities module in the same shared directory.
- The rendered elements in the UI should use updated identifiers that reflect the generic nature of the component rather than implying they are specific to the chat input.
- The container that wraps multiple file chips should likewise be renamed and moved.
- End-to-end scenarios should confirm that text, audio recordings, and file attachments can all be combined and submitted together in a single interaction.

## Why This Matters

This refactoring allows other widgets beyond the chat input to reuse the same file chip UI and related utilities without duplicating code. It also makes the codebase easier to maintain by consolidating file upload display logic in one shared location.
