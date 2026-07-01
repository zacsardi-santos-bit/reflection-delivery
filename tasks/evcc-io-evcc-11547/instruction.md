Update the codebase to use the actively maintained fork of the mock library for Go testing. Ensure all test files import the new library, regenerate mock files, and update the dependency manifest. Implement a local mock for the e-mobility interface within the charger module.

*   Update imports:
    *   Modify all test files to replace imports of the legacy mock library with `go.uber.org/mock/gomock`.

*   Implement local mock for charger:
    *   Define a function `NewMockEmobilityI(ctrl *gomock.Controller) *MockEmobilityI` in `charger/eebus_test_mock.go`.
    *   Ensure `NewMockEmobilityI` is implemented within the charger package.
    *   Use `go.uber.org/mock/gomock` for the mock framework in `charger/eebus_test_mock.go`.

*   Regenerate mock files:
    *   Regenerate `charger/eebus_test_mock.go` using `go.uber.org/mock/gomock`.
    *   Update `api/mock.go`, `core/loadpoint/mock.go`, and `core/vehicle/mock.go` to use `go.uber.org/mock/gomock` instead of `github.com/golang/mock/gomock`.

*   Update dependency manifest:
    *   Add `go.uber.org/mock` as a direct dependency in `go.mod`.
    *   Move the legacy mock library to an indirect dependency or remove it from direct dependencies.

*   Modify mock recorder methods:
    *   Ensure all mock recorder methods in the updated mock files use `any` as the parameter type instead of `interface{}`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.