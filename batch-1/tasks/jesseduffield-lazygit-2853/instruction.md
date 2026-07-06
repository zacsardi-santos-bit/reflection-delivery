Implement the ability to mark integration tests as demos, allowing them to be skipped during automated test runs and enabling caption display during recordings. Extend the test framework to support demo-specific features and add initial demo scenarios.

*   Update the `IntegrationTest` type:
    *   Add an `isDemo` bool field to the `IntegrationTest` struct.
    *   Add an `IsDemo` bool field to `NewIntegrationTestArgs`.
    *   Implement an `IsDemo() bool` method that returns the value of `isDemo`.

*   Modify the integration test runner:
    *   Check the `IsDemo()` method for each test.
    *   Skip execution of tests where `IsDemo()` returns true.

*   Enhance the `GuiDriver` interface:
    *   Add `SetCaption(string)` and `SetCaptionPrefix(string)` methods.

*   Extend the `TestDriver`:
    *   Implement `SetCaption(string)`, `SetCaptionPrefix(string)`, and `Wait(milliseconds int)` methods.
    *   Ensure `SetCaption` and `SetCaptionPrefix` delegate to the `GuiDriver`.

*   Update the `ViewDriver`:
    *   Implement `SetCaptionPrefix(prefix string) *ViewDriver` and `Wait(milliseconds int) *ViewDriver` methods for fluent chaining.

*   Enhance the `Shell` component:
    *   Add a `CreateNCommitsWithRandomMessages(n int) *Shell` method to generate commits with random messages and file names.

*   Create a new demo package:
    *   Location: `pkg/integration/tests/demo`.
    *   Include four exported `*IntegrationTest` variables: `Bisect`, `CherryPick`, `CommitAndPush`, and `InteractiveRebase`.
    *   Set `IsDemo: true` for each demo test.

*   Register demo tests:
    *   Add the demo tests to the global test list in `pkg/integration/tests/test_list.go`.

*   Update the default test configuration:
    *   Modify `test/default_test_config/config.yml` to set `inactiveBorderColor` to black and `showRandomTip` to false.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.