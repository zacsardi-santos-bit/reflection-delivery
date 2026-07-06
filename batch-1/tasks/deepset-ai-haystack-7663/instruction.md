Implement a new chat prompt builder component for Haystack that supports both static and dynamic prompt templates. Ensure it integrates with the component system and handles multi-message chat prompts effectively.

*   Implement the `ChatPromptBuilder` class in `haystack/components/builders/chat_prompt_builder.py`.
    *   Decorate the class with `@component`.
    *   Define the constructor `__init__` with parameters: `template`, `required_variables`, and `variables`, all defaulting to `None`.
    *   Set attributes: `self.template`, `self.required_variables`, `self._variables`, and `self._required_variables` based on constructor arguments.
    *   Raise `TemplateSyntaxError` if any user or system message in the provided template contains invalid Jinja2 syntax.

*   Register input and output sockets:
    *   Input sockets:
        *   "template": `Optional[List[ChatMessage]]`
        *   "template_variables": `Optional[Dict[str, Any]]`
        *   One socket per variable name from `variables` list or inferred from template if `variables` is not provided.
    *   Output socket:
        *   "prompt": `List[ChatMessage]`

*   Implement the `run` method:
    *   Accept parameters: `template`, `template_variables`, and `**kwargs`.
    *   Return a dictionary with key "prompt" and value as a list of rendered `ChatMessage` instances.
    *   Ensure `template_variables` dict values take precedence over `kwargs` values on key collision.
    *   Raise `ValueError` with message containing 'The ChatPromptBuilder requires a non-empty list of ChatMessage instances' if no template is set or if the resolved template is empty.
    *   Raise `ValueError` with message containing 'The ChatPromptBuilder expects a list containing only ChatMessage instances' if the template list contains non-`ChatMessage` objects.
    *   Raise `ValueError` listing missing required variables if any are absent from the combined template variables.
    *   Render only user and system messages using Jinja2; pass assistant messages unchanged.
    *   Render missing optional template variables as empty strings.

*   Implement `_validate_variables` method:
    *   Accept a `provided_variables` set.
    *   Raise `ValueError` if any variable in `required_variables` is missing from `provided_variables`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.