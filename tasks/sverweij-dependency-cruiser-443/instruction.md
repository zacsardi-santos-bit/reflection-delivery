Implement support for recording the originating "from" module path in each reachability annotation and use that information to correctly expand capture groups when computing reachability and validating rules.

*   Update the `addReachability` function in `src/enrich/derive/reachable/index.js`:
    *   Ensure each entry in a module's `reachable` array includes a `matchedFrom` field containing the source path of the specific 'from' module that triggered the reachability annotation.
    *   Ensure the `reachable` array entry structure includes `value` (boolean), `asDefinedInRule` (string), and `matchedFrom` (string).
    *   Populate `matchedFrom` on each reachable entry with the source path of the originating module.
    *   When a reachability rule uses regex capture groups in its `from.path` pattern, interpolate those captured values into the `to.path` pattern to determine if a module is in scope for that rule.

*   Update the module validation function in `src/validate/index.js`:
    *   Use the `matchedFrom` field from a module's reachable entries to extract capture groups from the originating 'from' module path.
    *   Apply those captured groups when evaluating whether the `to.path` and `to.pathNot` patterns in the rule match the current module.
    *   Ensure that if a module's reachable entry has `value: false`, `asDefinedInRule` matching a rule name, and `matchedFrom` containing a path that matches the rule's `from.path` pattern, and the module's own source path matches the interpolated `to.path`, the module validation returns `valid: false` with the matching rule name and severity.

*   Ensure the fixture at `test/validate/fixtures/rules.reachable.capturing-group.json`:
    *   Defines a forbidden rule named `capt-group` with `from.path` set to `^src/([^/]+)/index\.js$` and `to.path` set to `^src/$1/.+` with `reachable` set to `false`.
    *   Verifies that capture group interpolation works correctly during module validation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.