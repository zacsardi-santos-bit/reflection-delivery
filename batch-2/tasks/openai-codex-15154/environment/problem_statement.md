## Description

When an AI generates an image and saves it to disk, the chat interface shows a "Saved to:" entry in the conversation history. Currently, this entry only displays the parent directory (e.g., a temporary folder path), not the actual filename of the saved image. This makes it impossible for the user to know which specific file was generated.

Additionally, the path is displayed as a raw filesystem path rather than a standard file URI, which is less useful for users who want to open or reference the file directly.

## Expected Behavior

- The "Saved to:" line in the image generation history entry should display the full file URI, including the filename — not just the parent directory.
- If the path provided is a plain filesystem path, it should be converted to a standard file URI format.
- If the path is already provided as a file URI, it should be displayed as-is.

## Why This Matters

Users generating images through the AI assistant need to know exactly where the image was saved. Showing only the directory is not actionable — they need the full path and ideally a format they can use to open the file. Using a proper file URI format is a more standard, interoperable representation.
