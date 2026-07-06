Implement the ability to send usage analytics for Flutter's code size analysis feature. Ensure that an analytics event is recorded each time a code size analysis is performed on supported platforms, capturing which platform was analyzed.

*   Update the `SizeAnalyzer` class in `packages/flutter_tools/lib/src/base/analyze_size.dart`:
    *   Add an optional named parameter `flutterUsage` of type `Usage` to the constructor.
    *   Ensure `flutterUsage` is used to send analytics events when a size analysis is performed.

*   Implement analytics event dispatching for each platform:
    *   For Linux builds with `--analyze-size`, send a 'code-size-analysis' event with the label 'linux'.
    *   For macOS builds with `--analyze-size`, send a 'code-size-analysis' event with the label 'macos'.
    *   For Windows builds with `--analyze-size`, send a 'code-size-analysis' event with the label 'windows'.
    *   For Android APK builds using `buildGradleApp` with `codeSizeDirectory` set, send a 'code-size-analysis' event with the label 'apk'.

*   Ensure the status output after analysis completion includes platform-specific summary text:
    *   For Linux, include 'A summary of your Linux bundle analysis can be found at'.
    *   For macOS, include 'A summary of your macOS bundle analysis can be found at'.
    *   For Windows, include 'A summary of your Windows bundle analysis can be found at'.

*   Ensure the `SizeAnalyzer` constructor passes the `flutterUsage` instance to the analytics event, allowing events to be dispatched through the provided `Usage` object.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.