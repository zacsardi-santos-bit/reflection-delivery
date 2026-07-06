I'm working with a locally built Flutter engine and I've noticed that when the tooling can't find an engine build directory at the path I've configured, the error message it shows is confusing.

*   When the local engine tooling cannot find a Flutter engine build directory at the specified source path, the error message must contain the text 'No Flutter engine build directory found at: <path>' where <path> is the engine source path that was searched.

*   The old error message phrasing 'Unable to detect a Flutter engine build directory in <path>' must be replaced with the new format 'No Flutter engine build directory found at: <path>'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.