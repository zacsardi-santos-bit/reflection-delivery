Refactor the graph vertex code by extracting the parameter-processing logic into a standalone class. Implement this class to handle various parameter types and ensure it is testable independently. Additionally, rename a model component module for clarity and update all references accordingly.

*   Implement the `ParameterHandler` class in `langflow/graph/vertex/base.py`:
    *   Constructor: `__init__(vertex: Vertex, storage_service: StorageService) -> None`
        *   Initialize with a `Vertex` instance and a `StorageService` instance.
        *   Create a public `template_dict` attribute with dict-valued entries from `vertex.data["node"]["template"]`.
    *   Methods:
        *   `process_edge_parameters(edges: list) -> dict`
            *   Iterate over edges, skip those without `target_param`, and return a dict mapping `target_param` to the source vertex.
        *   `process_file_field(field_name: str, field: dict, params: dict) -> dict`
            *   Set `params[field_name]` based on `file_path`, `required`, and `list` attributes.
            *   Return the updated `params` dict.
        *   `should_skip_field(field_name: str, field: dict, params: dict) -> bool`
            *   Return `True` if `field_name` is in `params`, equals '_type', or `field['show']` is `False`.
            *   Return `False` if `field['show']` is `True`.
        *   `process_non_list_edge_param(field: dict, edge: Edge) -> Any`
            *   Return the source vertex for empty or non-dict fields.
            *   Return a new dict for single-entry dict fields.
        *   `handle_optional_field(field_name: str, field: dict, params: dict) -> None`
            *   Set or remove `params[field_name]` based on `required` and `default` attributes.
        *   `process_field_parameters() -> tuple[dict, list]`
            *   Process fields by type, handle errors, and return a tuple of `params_dict` and `load_from_db_fields_list`.
            *   Convert `str`, `int`, `float`, `bool`, `dict`, and `table` fields appropriately.
            *   Use `ast.literal_eval` for code fields, falling back on exceptions.
            *   Raise `ValueError` for invalid field types or table values.

*   Rename the module `langflow/components/models/openai.py` to `langflow/components/models/openai_chat_model.py`:
    *   Ensure `OpenAIModelComponent` is importable from `langflow.components.models.openai_chat_model`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.