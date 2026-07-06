Correct the numeric type inconsistencies in the codebase to eliminate compiler warnings and precision issues. Ensure that accessor methods return the appropriate types and maintain precision as specified.

*   Implement the `Token` class in `src/tokenizer.h`:
    *   `AsFloat()` should return a `float`.
    *   `AsDouble()` should return a `double` with full precision for 64-bit integer conversions.

*   Update the `DrawRectCommand` class in `src/command.h` (or `src/vkscript/command_parser.h`):
    *   `GetX()`, `GetY()`, `GetWidth()`, and `GetHeight()` should all return `float`.

*   Modify the `ClearDepthCommand` class in `src/command.h` (or `src/vkscript/command_parser.h`):
    *   `GetValue()` should return a `float`.

*   Adjust the `ClearStencilCommand` class in `src/command.h` (or `src/vkscript/command_parser.h`):
    *   `GetValue()` should return an `unsigned integer` type, such as `uint32_t`.

*   Revise the `ClearColorCommand` class in `src/command.h` (or `src/vkscript/command_parser.h`):
    *   `GetR()`, `GetG()`, `GetB()`, and `GetA()` should each return `float`.

*   Update the `ProbeCommand` class in `src/command.h` (or `src/vkscript/command_parser.h`):
    *   `GetR()`, `GetG()`, `GetB()`, `GetA()`, `GetX()`, `GetY()`, `GetWidth()`, and `GetHeight()` should all return `float`.

*   Modify the `PatchParameterVerticesCommand` class in `src/command.h` (or `src/vkscript/command_parser.h`):
    *   `GetControlPointCount()` should return an `unsigned integer` type, such as `uint32_t`.

*   Update the `PipelineData` class in `src/command_data.h` (or similar):
    *   `GetDepthBiasConstantFactor()`, `GetDepthBiasClamp()`, `GetDepthBiasSlopeFactor()`, `GetLineWidth()`, `GetMinDepthBounds()`, and `GetMaxDepthBounds()` should all return `float`.

*   Adjust the `Tolerance` struct in `src/command.h` or `src/vkscript/command_parser.h` (or similar):
    *   The `value` field should be of type `double`.
    *   The `is_percent` field should remain of type `bool`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.