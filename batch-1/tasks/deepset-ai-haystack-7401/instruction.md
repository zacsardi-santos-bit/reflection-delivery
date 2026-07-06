Implement an LLM-based evaluator component in Haystack to assess pipeline outputs using a language model. Ensure the component is serializable, deserializable, and importable, with thorough configuration validation and consistent output structure. Follow the specified method signatures and validation rules.

*   Implement the LLMEvaluator class in `haystack/components/evaluators/llm_evaluator.py`.
    *   Decorate with `@component` and export from `haystack/components/evaluators/__init__.py`.
    *   Add to `__all__` in `__init__.py`.

*   Define the `__init__` method with parameters:
    *   `instructions: str`
    *   `inputs: List[Tuple[str, Type[List]]]`
    *   `outputs: List[str]`
    *   `examples: List[Dict[str, Any]]`
    *   `api: str = "openai"`
    *   `api_key: Secret = Secret.from_env_var("OPENAI_API_KEY")`
    *   Store these as instance attributes.

*   Implement API and key handling:
    *   Create an `OpenAIGenerator` instance as `self.generator` if `api='openai'`.
    *   Ensure `generator.client.api_key` is the resolved API key string.
    *   Raise `ValueError` if the API key is missing or if `api` is unsupported.

*   Validate inputs in `__init__`:
    *   Raise `ValueError` for invalid `inputs`, `outputs`, or `examples` with specific criteria for each.

*   Implement serialization methods:
    *   `to_dict()` returns a dictionary with specific keys and serialized attributes.
    *   `from_dict(cls, data)` deserializes and returns an initialized `LLMEvaluator`.

*   Implement `run(self, **inputs)`:
    *   Validate input list lengths.
    *   Call the generator, parse JSON replies, and return structured results.

*   Implement `prepare_template()` to return a formatted prompt string.

*   Implement validation methods:
    *   `validate_input_parameters(expected, received)` checks for key presence and list consistency.
    *   `validate_outputs(expected, received)` parses JSON and checks for expected keys.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.