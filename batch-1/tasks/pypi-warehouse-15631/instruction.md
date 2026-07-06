Refactor the package upload system by separating metadata parsing and validation from HTTP form handling. Implement a dedicated metadata module to handle metadata parsing and validation, and create a separate forms module for form handling logic.

*   Create a new module at `warehouse/forklift/forms.py`:
    *   Implement `_validate_pep440_version(form, field) -> None` to validate PEP 440 version strings. Raise `wtforms.validators.ValidationError` for invalid strings.
    *   Implement `UploadForm`, a WTForms Form subclass with `full_validate() -> None`:
        *   Raise `ValidationError` if no digest field is present.
        *   Raise `ValidationError` if `filetype` is "bdist_wheel" and `pyversion` is absent.
        *   Raise `ValidationError` if `filetype` is "sdist" and `pyversion` is set to a non-source value.

*   Create a new module at `warehouse/forklift/metadata.py`:
    *   Implement `parse(data: bytes | None, form_data: MultiDict | None = None, backfill: bool = False) -> packaging.metadata.Metadata`:
        *   Raise `NoMetadataError` if both `data` and `form_data` are `None`.
        *   Return a `Metadata` object for valid inputs.
        *   Raise `ExceptionGroup` of `InvalidMetadata` for validation failures, with `.field` attribute naming the invalid field.
        *   Reject unsupported metadata versions and versions with local identifiers.
        *   Enforce field length limits using `_LENGTH_LIMITS`.
        *   Validate email formats and classifier validity.
        *   Reject non-http/https URLs and direct URL dependencies.
    *   Implement `parse_form_metadata(data: MultiDict) -> packaging.metadata.Metadata`:
        *   Convert keywords to lists, project URLs to dictionaries, and empty strings to `None`.
        *   Raise `ExceptionGroup` for duplicate values.
    *   Define `InvalidMetadata` and `NoMetadataError` exception classes.
    *   Define `_LENGTH_LIMITS` with `{'summary': 512}`.
    *   Re-export `Metadata` from `packaging.metadata`.

*   Update `warehouse/forklift/legacy.py`:
    *   Modify `_construct_dependencies(meta: packaging.metadata.Metadata, types: dict) -> Iterator[Dependency]` to accept a `Metadata` object and normalize requirement specifiers.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.