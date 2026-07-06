Implement functionality in the Flutter web build tooling to ensure that a local fallback Roboto font is included in release builds configured to use locally-bundled assets. This should occur automatically without any additional developer configuration.

*   Modify the `WebReleaseBundle` class in `packages/flutter_tools/lib/src/build_system/targets/web.dart` to support the following:
    *   Detect when the local CanvasKit flag is set to 'true'.
    *   Add a Roboto font entry to the `FontManifest.json` file in the output assets directory.
        *   The entry must have:
            *   'family' set to 'Roboto'.
            *   'fonts' set to a list containing one object with 'asset' set to 'fonts/fallback/Roboto-Regular.ttf'.
    *   Copy the Roboto font file from the Flutter engine source tree:
        *   Source path: `engine/src/flutter/txt/third_party/fonts/Roboto-Regular.ttf`.
        *   Destination path: `assets/fonts/fallback/Roboto-Regular.ttf` in the output directory.
    *   Ensure the Roboto font bundling only occurs when the local CanvasKit flag is 'true'.
    *   Prevent duplicate entries in `FontManifest.json` if Roboto is already present when the local CanvasKit flag is true.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.