Implement changes to the maeparser library to ensure it compiles cleanly under strict compiler warnings, specifically addressing issues with implicit type conversions and missing virtual destructors.

*   Update the `BoolProperty` type:
    *   Ensure `BoolProperty` is a distinct non-bool integral type.
    *   Support explicit casting via `static_cast<BoolProperty>(true)` and `static_cast<BoolProperty>(false)`.
    *   Allow equality comparisons between `BoolProperty` values without implicit conversion.

*   Modify `parse_value<BoolProperty>`:
    *   Ensure it returns `static_cast<BoolProperty>(true)` when parsing '1'.
    *   Ensure it returns `static_cast<BoolProperty>(false)` when parsing '0'.
    *   Signature: `parse_value<BoolProperty>(Buffer& b) -> BoolProperty`.

*   Update `IndexedBoolProperty` (IndexedProperty<BoolProperty>):
    *   `operator[]` must return a `BoolProperty` value equal to `static_cast<BoolProperty>(true)` for defined true-valued entries.
    *   `operator[]` must throw `std::runtime_error` for null or undefined entries.
    *   Ensure `isDefined(index)` returns true for defined entries and false for undefined entries.
    *   Signatures:
        *   `BoolProperty& operator[](size_type index)`
        *   `const BoolProperty& operator[](size_type index) const`
        *   `bool isDefined(size_type index) const`

*   Adjust `parse_value<int>`:
    *   Ensure it correctly parses the string representation of the minimum integer value (`std::numeric_limits<int>::min()`) and returns that exact integer value.
    *   Signature: `parse_value<int>(Buffer& b) -> int`.

*   Ensure all classes with virtual functions have virtual destructors:
    *   Specifically, declare a virtual destructor for `IndexedBlockBuffer` in `MaeParser.hpp`.
    *   Signature: `virtual ~IndexedBlockBuffer()`.

*   Update index-keyed property maps in user-facing demo code:
    *   Use `size_t` instead of `int` as the key type to avoid signed/unsigned comparison warnings.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.