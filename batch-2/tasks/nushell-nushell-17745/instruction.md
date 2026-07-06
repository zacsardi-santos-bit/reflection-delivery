I'd like to improve the nushell test infrastructure in two ways.

*   Must add a public `assert_contains` function to the `nu_test_support` crate that accepts a needle and a haystack, and panics with a descriptive message (showing both haystack and needle) if the haystack does not contain the needle.

*   The `assert_contains` function must be exported through the `nu_test_support::prelude` module so that tests using `nu_test_support::prelude::*` can call it directly.

*   The `assert_contains` function must work with string slices, owned `String` values, slices, `Vec`, and other standard collection types as the haystack argument — accepting both `&str` needles against `String` haystacks and vice versa.

*   Must add an `inherit_path` method to the `NuTester` struct in `crates/nu-test-support/src/tester/mod.rs`. The method must read the `PATH` environment variable from the running process and add it to the test engine state. It must return `Self` to allow method chaining.

*   Must add a `Container` trait in `crates/nu-utils/src/container.rs` with an associated `Item` type and a `contains(&self, item: &Self::Item) -> bool` method. The `Container` trait must be implemented for at minimum: `str` (Item = str), `String` (Item = str), arrays `[T; N]`, slices `[T]`, `Vec<T>`, `HashSet<T>`, `BTreeSet<T>`, `HashMap<K, V>` (contains by key), `BTreeMap<K, V>` (contains by key), and range types. Reference implementations for `&C where C: Container` must also be provided.

*   The `container` module must be publicly exported from `crates/nu-utils/src/lib.rs`.


*   Interface details: Type: Function
Name: assert_contains
Location: crates/nu-test-support/src/lib.rs
Signature: assert_contains<H, N>(needle: N, haystack: H) where H: Container + Debug, N: Borrow<H::Item>, H::Item: Debug
Description: Asserts that the haystack contains the given needle. Panics with a message showing both the haystack and the needle if the check fails. Must be exported from the crate root and re-exported in the `prelude` module.

Type: Trait
Name: Container
Location: crates/nu-utils/src/container.rs
Description: A minimal abstraction for membership checks across container-like types. Has an associated type `Item: ?Sized` and a method `contains(&self, item: &Self::Item) -> bool`. Must be implemented for: `str` (Item = str), `String` (Item = str), `[T; N]`, `[T]`, `Vec<T>`, `VecDeque<T>`, `LinkedList<T>`, `HashSet<T>`, `BTreeSet<T>`, `HashMap<K, V>` (contains by key), `BTreeMap<K, V>` (contains by key), `Range<T>`, `RangeInclusive<T>`, `RangeFrom<T>`, `RangeTo<T>`, `RangeToInclusive<T>`, and `&C where C: Container`. The module must be publicly exported from `crates/nu-utils/src/lib.rs` as `pub mod container`.

Type: Method
Name: inherit_path
Location: crates/nu-test-support/src/tester/mod.rs
Signature: inherit_path(self) -> Self
Description: Method on the `NuTester` struct. Reads the `PATH` environment variable from the running process using `env::var("PATH")` and adds it to the test engine state. Returns `Self` for method chaining, matching the builder pattern of other `NuTester` methods like `env()` and `cwd()`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.