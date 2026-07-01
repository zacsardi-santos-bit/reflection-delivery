Implement enhancements to the zsh-autosuggestions plugin by addressing two main issues: performance optimization through optional widget re-binding and improving Ctrl-C behavior in async mode.

*   Add a configuration option to control widget re-binding:
    *   Introduce the variable `ZSH_AUTOSUGGEST_MANUAL_REBIND` in `src/config.zsh`.
    *   When `ZSH_AUTOSUGGEST_MANUAL_REBIND` is set, prevent automatic re-binding of widgets on each precmd.
    *   Require users to manually call `_zsh_autosuggest_bind_widgets()` to apply widget list changes.
    *   Ensure that after manual re-binding, the updated widget bindings operate correctly.

*   Refactor async mode to handle Ctrl-C correctly:
    *   Ensure that when async suggestions are enabled and a suggestion fetch is triggered, pressing Ctrl-C terminates the current prompt.
    *   Display the aborted input on its own line followed by a new prompt.
    *   Apply this behavior only for zsh version 5.0.8 and above.

*   Update the function `_zsh_autosuggest_bind_widgets`:
    *   Define `_zsh_autosuggest_bind_widgets()` in `src/bind.zsh`.
    *   Ensure it binds all autosuggest widgets according to the current configuration when called.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.