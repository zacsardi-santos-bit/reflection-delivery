Implement two utility packages: a pattern-matching utility and a type-safe concurrent dictionary. The pattern-matching utility should efficiently find pre-registered strings within larger text using two implementations: a simple one for small pattern sets and a prefix-tree-based one for large sets. The concurrent dictionary should maintain type safety across goroutines and support standard operations.

*   Implement the `PatternFinder` interface in `pkg/common/patternfinder/interface.go` with the following methods:
    *   `AddPattern(pattern string, outcome T) error`: Return `nil` on success, `ErrPatternAlreadyExists` if the pattern is already registered.
    *   `GetPattern(pattern string) (T, error)`: Return the stored value and `nil` on success, or a zero value and `ErrPatternNotFound` if not found.
    *   `DeletePattern(pattern string) (T, error)`: Return the stored value and `nil` on success; subsequent `GetPattern` should return `ErrPatternNotFound`.
    *   `Match(searchTarget []rune) *PatternMatchResult[T]`: Perform longest-prefix matching, returning `nil` if no match is found.

*   Implement `NewNaivePatternFinder` in `pkg/common/patternfinder/naive.go` and `NewTriePatternFinder` in `pkg/common/patternfinder/trie.go`:
    *   Both must satisfy the `PatternFinder` interface and handle add/get/delete lifecycle, duplicate-add errors, missing-key errors, and prefix matching.

*   Implement `FindAllWithStarterRunes` in `pkg/common/patternfinder/finder.go`:
    *   Scan `searchText` for patterns using `starterRunes`. Include the start of the text if `includeFirst` is true. Return a nil slice if no matches are found.

*   Implement the `TypedDict` struct in `pkg/common/typeddict/typeddict.go` with the following methods:
    *   `NewTypedDict[T any]() *TypedDict[T]`: Create a new, empty dictionary.
    *   `Set[T any](m *TypedDict[T], key string, value T)`: Store a value for a key, overwriting any prior value.
    *   `Get[T any](dict *TypedDict[T], key string) (T, bool)`: Return (value, true) if the key exists, (zero value of T, false) if not.
    *   `Delete[T any](m *TypedDict[T], key string)`: Remove the key from the dictionary.
    *   `GetOrDefault[T any](m *TypedDict[T], key string, defaultValue T) T`: Return the value if the key exists, otherwise return `defaultValue`.
    *   `GetOrSetFunc[T any](m *TypedDict[T], key string, genFunc func() T) T`: Return the existing value if present, otherwise call `genFunc`, store, and return the result.

*   Ensure all `TypedDict` operations are safe for concurrent use by multiple goroutines without data races.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.