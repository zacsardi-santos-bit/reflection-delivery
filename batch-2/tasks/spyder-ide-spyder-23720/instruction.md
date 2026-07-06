Create a shared module for Spyder plugin test utilities to centralize and streamline test setup. Ensure that the module is importable and provides necessary fixtures for plugin registration and teardown.

*   Implement the module `spyder/api/plugins/tests.py` to be importable without errors.
    *   Ensure `from spyder.api.plugins.tests import *` works correctly.
    *   Declare `__all__ = ["main_window_mock", "plugins_cls", "register_fixture"]`.

*   Implement the `MainWindowMock` class within the module.
    *   Subclass `QMainWindow` and integrate with Spyder's plugin registry.
    *   Define methods:
        *   `register_plugin(plugin_class) -> plugin`: Register and return the plugin.
        *   `unregister_plugin(plugin) -> None`: Unregister the plugin.
        *   `get_plugin(plugin_name, error=False) -> plugin`: Retrieve a plugin.
        *   `is_plugin_available(plugin_name) -> bool`: Check plugin availability.

*   Implement the `main_window_mock` fixture.
    *   Scope: session
    *   Signature: `main_window_mock(qapp) -> MainWindowMock`
    *   Create and yield a `MainWindowMock` instance.
    *   On teardown, reset configuration and plugin registry, delete the window, and perform garbage collection.

*   Implement the `plugins_cls` fixture.
    *   Scope: session
    *   Signature: `plugins_cls() -> Iterable[Tuple[str, type]]`
    *   Abstract fixture to be overridden in `conftest.py`.
    *   Raise `NotImplementedError` if not overridden.

*   Implement the `register_fixture` fixture.
    *   Scope: session
    *   Autouse: True
    *   Signature: `register_fixture(request: SubRequest, plugins_cls) -> None`
    *   Dynamically register session-scoped plugin fixtures for each `(fixture_name, plugin_cls)` in `plugins_cls`.
    *   Insert a `FixtureDef` for each pair into `request._fixturemanager._arg2fixturedefs[fixture_name]`.
    *   Each generated fixture must:
        *   Take `main_window_mock` as an argument.
        *   Call `main_window_mock.register_plugin(plugin_cls)`, yield the plugin.
        *   On teardown, call `main_window_mock.unregister_plugin(plugin)`.

*   Ensure the module loads without errors when used with a `conftest.py` that overrides `plugins_cls`, even if no test requests the dynamically created plugin fixtures.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.