I'd like to improve the diagnostic messages emitted by a few of the nursery lint rules.

*   The useArraySome lint rule must emit updated diagnostic messages. For the filter-then-length-check pattern, the first note must read: "This expression uses .filter() and then checks whether the result is empty." and the second note must read: ".some() matches that intent better and can stop as soon as it finds a match."

*   For the findIndex-compared-to-negative-one pattern (FIXABLE), useArraySome must emit: first note "This expression uses .findIndex() to test whether any array element matches." and second note ".some() makes that intent clearer because it returns a boolean directly."

*   For the findLastIndex-compared-to-negative-one pattern (FIXABLE), useArraySome must emit: first note "This expression uses .findLastIndex() to test whether any array element matches." and second note ".some() makes that intent clearer because it returns a boolean directly."

*   For the find-family-used-as-boolean pattern (no autofix available), useArraySome must emit three notes: "This expression uses a .find()-style call as a boolean test.", ".some() makes that intent clearer because it returns a boolean directly.", and "Use .some() instead."

*   For the find-existence-comparison pattern (no autofix available), useArraySome must emit three notes: "This expression uses .find() only to check whether an array element exists.", ".some() makes that intent clearer because it returns a boolean directly.", and "Use .some() if you only need to know whether any element matches."

*   For the findLast-existence-comparison pattern (no autofix available), useArraySome must emit three notes: "This expression uses .findLast() only to check whether an array element exists.", ".some() makes that intent clearer because it returns a boolean directly.", and "Use .some() if you only need to know whether any element matches, regardless of order."

*   When useArraySome has an autofix action available (FIXABLE cases), it must emit only two diagnostic notes (problem description and reason); the additional how-to-fix note must be omitted. When no autofix is available, three notes must be emitted.

*   The useFind lint rule must emit updated diagnostic messages for the filter-then-index-access pattern. The first note must read: "This call uses Array#filter() to retrieve a single matching element." The second note must read: "Array#find() expresses that intent more clearly and can stop once it finds the first match." A third note must read: "Use Array#find() instead."

*   The useRegexpExec lint rule must emit updated diagnostic messages for the String#match-with-regexp pattern. The first note must read: "This call uses String#match() with a regular expression." The second note must read: "RegExp#exec() communicates the regex operation more directly and keeps this pattern consistent." A third note must read: "Use RegExp#exec() instead."


*   Interface details: Type: RuleImplementation
Name: UseArraySome
Location: crates/biome_js_analyze/src/lint/nursery/use_array_some.rs
Description: The diagnostic method of the UseArraySome rule must be updated to emit new message strings. The rule emits two notes when an autofix action is available (FIXABLE cases), and three notes when no autofix action is available. The exact required message strings are documented in requirements.json.

Type: RuleImplementation
Name: UseFind
Location: crates/biome_js_analyze/src/lint/nursery/use_find.rs
Description: The diagnostic method of the UseFind rule must be updated to emit three notes with specific new message strings as documented in requirements.json.

Type: RuleImplementation
Name: UseRegexpExec
Location: crates/biome_js_analyze/src/lint/nursery/use_regexp_exec.rs
Description: The diagnostic method of the UseRegexpExec rule must be updated to emit three notes with specific new message strings as documented in requirements.json.

Type: SnapshotFile
Name: invalid.js.snap (useArraySome)
Location: crates/biome_js_analyze/tests/specs/nursery/useArraySome/invalid.js.snap
Description: Snapshot file containing the expected linter output for useArraySome on invalid.js. This file has already been updated with the new expected messages; the implementation must produce output matching this snapshot.

Type: SnapshotFile
Name: invalid.ts.snap (useFind)
Location: crates/biome_js_analyze/tests/specs/nursery/useFind/invalid.ts.snap
Description: Snapshot file containing the expected linter output for useFind on invalid.ts. Already updated; implementation must match.

Type: SnapshotFile
Name: invalid.js.snap (useRegexpExec)
Location: crates/biome_js_analyze/tests/specs/nursery/useRegexpExec/invalid.js.snap
Description: Snapshot file containing the expected linter output for useRegexpExec on invalid.js. Already updated; implementation must match.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.