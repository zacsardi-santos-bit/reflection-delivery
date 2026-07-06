I noticed that when the AI generates an image and saves it to disk, the chat history shows a "Saved to:" entry — but it only displays the parent folder, not the actual filename of the generated image.

*   When an image generation completion event includes a saved path, the chat history cell must display the full file URI (e.g., 'file:///tmp/ig-1.png') in a 'Saved to:' line — not just the parent directory.

*   When the saved path in an image generation event is already a file URI (starting with 'file://'), the value must be passed through and displayed as-is in the history cell.

*   When the saved path is a plain filesystem path, the rendering layer must convert it to a file:// URI before displaying; if the conversion fails, the original value must be used as a fallback.

*   After handling an ImageGenerationEnd event, exactly one history cell must be added to the chat history.

*   The snapshot file at 'codex-rs/tui/src/chatwidget/snapshots/codex_tui__chatwidget__tests__image_generation_call_history_snapshot.snap' must be updated so its rendered output shows '  └ Saved to: file:///tmp/ig-1.png' (the full file URI, not just the directory).

*   The snapshot file at 'codex-rs/tui_app_server/src/chatwidget/snapshots/codex_tui__chatwidget__tests__image_generation_call_history_snapshot.snap' must likewise be updated to show the full file URI in the 'Saved to:' line.

*   The snapshot file at 'codex-rs/tui_app_server/src/chatwidget/snapshots/codex_tui_app_server__chatwidget__tests__image_generation_call_history_snapshot.snap' must likewise be updated to show the full file URI in the 'Saved to:' line.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.