Implement the `Authentication` class to support multiple credentials in accordance with the KMIP specification. Update the class to handle a list of credentials, ensuring proper validation, reading, writing, and comparison functionalities.

*   Modify the `Authentication` class constructor:
    *   Accept an optional `credentials` keyword argument.
    *   Default `credentials` to an empty list if no argument is provided.
    *   Raise a `TypeError` with the message "Credentials must be a list of Credential structs." if `credentials` is not a list and not `None`.
    *   Raise a `TypeError` with a message in the format "Credentials must be a list of Credential structs. Item 2 has type: <type>" if the list contains a non-Credential item (1-based index).

*   Implement the `credentials` property:
    *   The getter should return the internal list of Credential structs.
    *   The setter should apply the same validation as the constructor.

*   Update the `read` method:
    *   Read one or more Credential structs from a binary stream and populate the `credentials` list.
    *   Support reading a single `UsernamePasswordCredential`, a single `DeviceCredential`, and multiple credentials of mixed types.
    *   Raise a `ValueError` with the message "Authentication encoding missing credentials." if no credentials are found in the stream.

*   Update the `write` method:
    *   Serialize all credentials in the `credentials` list to a binary stream.
    *   Raise a `ValueError` with the message "Authentication struct missing credentials." if the `credentials` list is empty.

*   Implement equality and inequality methods:
    *   `__eq__`: Return `True` if another `Authentication` instance has an identical `credentials` list; otherwise, return `NotImplemented`.
    *   `__ne__`: Return `True` if another `Authentication` instance has a different `credentials` list; otherwise, return `NotImplemented`.

*   Implement string representation methods:
    *   `__repr__`: Return a string in the format `Authentication(credentials=[<repr of each Credential>, ...])`.
    *   `__str__`: Return a string equivalent to `str({'credentials': [str(credential_1), str(credential_2), ...]})`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.