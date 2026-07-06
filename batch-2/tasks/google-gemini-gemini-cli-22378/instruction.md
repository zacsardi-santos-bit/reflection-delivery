I'm trying to use the current Flash preview model with the Gemini CLI by specifying it via the model selection flag, but the CLI keeps rejecting it as invalid.

*   When 'gemini-2.5-flash-preview' is passed as the model name via the --model command-line argument, loadCliConfig must produce a configuration where getModel() returns 'gemini-2.5-flash-preview'.

*   The model value provided via the --model command-line argument must take priority over any model value specified in settings, including when the model is 'gemini-2.5-flash-preview'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.