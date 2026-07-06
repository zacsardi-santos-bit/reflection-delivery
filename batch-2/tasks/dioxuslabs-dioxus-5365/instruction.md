I'm using a reactive store system with nested fields, and I've hit a frustrating bug: when I write to a deeply nested field through a child accessor, components that subscribed to the parent value don't re-render.

*   When a component subscribes to a full parent-level store value and code later writes to a nested child field through a lens accessor, the parent-subscribed component must be re-rendered to reflect the change.

*   When writing to a nested child lens, only subscribers of the written path and ancestor paths (that have deep subscriptions) are re-rendered; components subscribed to unrelated sibling paths at any level must NOT be re-rendered.

*   Writes must propagate upward through arbitrarily deep nesting: writing to a grandchild path must re-render a component subscribed to the grandparent, and writing to a great-grandchild must re-render a component subscribed to the great-grandparent.

*   When writing to a specific index in a Vec store, components subscribed to the whole Vec must NOT be re-rendered (index writes are shallow); only components subscribed to that specific index are notified.

*   When calling push on a Vec store, components subscribed to the whole Vec must be re-rendered. Components subscribed only to a previously existing element index must NOT be re-rendered.

*   When calling push on a nested Vec field (inside a struct), the component subscribed to the parent struct must be re-rendered, but components subscribed to unrelated sibling fields of that struct must NOT be re-rendered.

*   A ReadSignal created from a child store lens must surface exactly one parent deep subscriber when its subscriber list is traversed via visit(). After removing that subscriber via remove(), a subsequent traversal must return no subscribers.

*   The StoreSubscriptions type in packages/stores/src/subscriptions.rs must expose pub(crate) methods: new() -> Self, deep_subscribers(key: &[PathKey]) -> Subscribers (subscribes to the exact path and all descendants), shallow_subscribers(key: &[PathKey]) -> Subscribers (subscribes to only the exact path), and mark_dirty(key: &[PathKey]) (marks the path and all descendants dirty while also notifying ancestor deep subscribers).

*   A unit test named mark_dirty_marks_descendants_without_remarking_ancestors must exist in packages/stores/src/subscriptions.rs inside a #[cfg(test)] mod tests block. This test must verify that calling mark_dirty(&[1]) notifies: a deep subscriber at the root path &[], a deep subscriber at path &[1, 2], and a shallow subscriber at path &[1, 2, 3], each exactly once (no double-notifications).


*   Interface details: Type: TypeAlias
Name: GlobalStore<T>
Location: packages/stores/src/lib.rs
Signature: type GlobalStore<T> = Global<Store<T>>
Description: A type alias for a global reactive store holding a value of type T. Declared as static and initialized with Global::new(fn). Accessed via .resolve() which returns Store<T>.

Type: DerivesMacro
Name: Store
Location: packages/stores/src/ (proc-macro crate, re-exported via dioxus_stores)
Description: A derive macro applied to structs. For each field of type F, generates an accessor method with the same name as the field that returns Store<F>. Supports nested structs and Vec fields.

Type: Struct
Name: StoreSubscriptions
Location: packages/stores/src/subscriptions.rs
Description: Internal subscription tree for a store. Tracks which reactive contexts are subscribed to which paths with either shallow or deep subscription depth.
Signature:
  new() -> Self
  track(key: &[PathKey])                          — shallow track (subscribe to exactly this path)
  track_deep(key: &[PathKey])                     — deep track (subscribe to this path and all descendants)
  mark_dirty(key: &[PathKey])                     — mark this path and all descendants dirty; also notifies ancestor deep subscribers
  mark_node_dirty(key: &[PathKey])                — mark exactly this node dirty plus ancestor deep subscribers
  shallow_subscribers(key: &[PathKey]) -> Subscribers  — returns Subscribers handle for shallow subscription at key
  deep_subscribers(key: &[PathKey]) -> Subscribers     — returns Subscribers handle for deep subscription at key
  mark_dirty_at_and_after_index(key: &[PathKey], index: usize)

Type: TypeAlias
Name: PathKey
Location: packages/stores/src/subscriptions.rs
Signature: type PathKey = u16
Description: An integer key identifying one segment of a store path. Used as slices (&[PathKey]) to address nodes in the subscription tree.

Note: A unit test named `mark_dirty_marks_descendants_without_remarking_ancestors` must be present in packages/stores/src/subscriptions.rs inside a `#[cfg(test)] mod tests { ... }` block. The test must call StoreSubscriptions::new(), subscribe three ReactiveContext instances (root at deep &[], child at deep &[1,2], leaf at shallow &[1,2,3]), call mark_dirty(&[1]), and assert each was notified exactly once.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.