Implement support for AWS SageMaker Model Cards in the moto library to enable offline unit testing. Ensure the mock implementation covers the full lifecycle of a Model Card, including creation, updating, describing, listing, deleting, and tag management, while handling error cases appropriately.

*   Implement the `create_model_card` operation:
    *   Accept parameters: model card name, status, content string, optional security config (with KMS key ID), and optional tags.
    *   Return a model card ARN in the format `arn:aws:sagemaker:{region}:{account_id}:model-card/{name}`.
    *   Raise `ConflictException` with the message 'Modelcard {name} already exists' if a model card with the same name exists.

*   Implement the `update_model_card` operation:
    *   Accept parameters: model card name, status, and content.
    *   Create a new version of the model card, incrementing the version number.
    *   Raise `ResourceNotFound` with the message 'Modelcard {name} does not exist.' if the model card does not exist.

*   Implement tag management for model cards:
    *   Allow tags to be set at creation, listed via `list_tags`, added via `add_tags`, and removed via `delete_tags`.
    *   Ensure tags persist when a model card is updated.

*   Implement the `list_model_cards` operation:
    *   Return a `ModelCardSummaries` list with each summary including `ModelCardName`, `ModelCardArn`, and `ModelCardStatus`.
    *   Support filtering by `NameContains`, `ModelCardStatus`, `CreationTimeBefore`, and `CreationTimeAfter`.
    *   Support sorting by `SortOrder` ('Ascending' by default, 'Descending') and `SortBy` ('Name' or creation time).

*   Implement the `list_model_card_versions` operation:
    *   Accept a model card name and return a `ModelCardVersionSummaryList`.
    *   Include `ModelCardVersion`, `CreationTime`, and `LastModifiedTime` for each version.
    *   Support filtering by `ModelCardStatus` and sorting by `SortOrder`.
    *   Raise `ResourceNotFound` for unknown model card names.

*   Implement the `describe_model_card` operation:
    *   Accept a model card name and an optional version number.
    *   Return details including `ModelCardArn`, `ModelCardName`, `ModelCardVersion`, `Content`, `ModelCardStatus`, `SecurityConfig`, `CreatedBy`, and `LastModifiedBy`.
    *   Default to the latest version if no version is specified.
    *   Raise `ResourceNotFound` with the message 'Modelcard with name {name} and version: {version} does not exist' for non-existent versions.

*   Implement the `delete_model_card` operation:
    *   Accept a model card name and remove it from listings.
    *   Raise `ResourceNotFound` if the model card does not exist.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.