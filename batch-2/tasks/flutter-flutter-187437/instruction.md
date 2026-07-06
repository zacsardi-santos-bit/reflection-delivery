Update the `ReleaseAssetServer` class to restrict access to certain files when serving HTTP requests. Ensure that only Dart source files are served from the project root and Flutter SDK root directories, while other files fall back to the `index.html` response.

*   Implement the `ReleaseAssetServer` class in `packages/flutter_tools/lib/src/isolated/release_asset_server.dart`.
    *   Ensure the constructor accepts a base URI and the following named parameters: `fileSystem`, `platform`, `flutterRoot`, `webBuildDirectory`, and `needsCoopCoep`.
    *   Implement the `handle` method with the signature `handle(Request request) -> Future<Response>`.
*   Serve Dart source files:
    *   For requests targeting Dart files (with `.dart` extension) in the project root directory, serve the file directly with HTTP 200 status.
    *   For requests targeting Dart files in the Flutter SDK root directory, serve the file directly with HTTP 200 status.
*   Restrict non-Dart files:
    *   For requests targeting non-Dart files in the project root directory, respond with the contents of the `index.html` fallback from the web build output directory.
    *   For requests targeting non-Dart files in the Flutter SDK root directory, respond with the contents of the `index.html` fallback from the web build output directory.
*   Maintain unrestricted access:
    *   Ensure files in the `webBuildDirectory` are served without any file extension restrictions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.