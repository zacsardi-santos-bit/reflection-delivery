## Description

Excalidraw currently uses hardcoded internal constants to control how inserted images are handled — specifically the maximum dimensions images are resized to and the maximum file size that is accepted. Host applications that embed Excalidraw have no way to override these values to match their own requirements.

## Expected Behavior

- Host applications should be able to pass image configuration through the component's props, specifying a maximum dimension for resizing and a maximum allowed file size.
- When an image is already within the configured dimension limit, it should be returned as-is without any reprocessing (no unnecessary resize).
- When an image file exceeds the host-configured size limit, the user should see a clear error message stating the file is too large and indicating the specific size limit in megabytes.

## Why This Matters

Different products embedding Excalidraw have different needs for image constraints. Some may want stricter limits to control bandwidth or storage, while others may allow larger images. Hardcoding these values forces all embedders to accept the same behavior. Additionally, the current resize logic always attempts to process images even when they are already small enough — returning the original file without reprocessing would avoid unnecessary work.
