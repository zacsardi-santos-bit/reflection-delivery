Implement the ability to explicitly control the parent relationship of spans in the tracing system. Extend the span! macro to allow specifying a parent span or creating a root span, regardless of the current execution context. Update the Subscriber interface to accommodate these changes.

*   Update the span! macro:
    *   Accept an optional `parent:` named parameter before the span name.
        *   Valid values: `None` (explicit root), a reference to an existing `Span`, or an `Option<Id>` value.
    *   Ensure compatibility with `target:`, `level:`, field arguments, and trailing commas in all valid combinations.
    *   When `parent: None` is specified:
        *   Create a span using `Attributes::new_root`.
        *   Ensure `Attributes::is_root()` returns `true` and `Attributes::is_contextual()` returns `false`.
    *   When an explicit parent is specified:
        *   Create a span using `Attributes::child_of`.
        *   Ensure `Attributes::is_contextual()` returns `false` and `Attributes::parent()` returns `Some(&id)`.
    *   When no `parent:` is specified:
        *   Create a span using `Attributes::new`.
        *   Ensure `Attributes::is_contextual()` returns `true`, `Attributes::is_root()` returns `false`, and `Attributes::parent()` returns `None`.

*   Modify `Subscriber::new_span` method:
    *   Change signature to `fn new_span(&self, span: &span::Attributes) -> span::Id`.

*   Update `span::Attributes` struct:
    *   Expose methods: `metadata() -> &Metadata`, `values() -> &field::ValueSet`, `is_root() -> bool`, `is_contextual() -> bool`, and `parent() -> Option<&Id>`.
    *   Ensure `span::Attributes` is publicly accessible as `tokio_trace::span::Attributes`.

*   Update `span::Id` struct:
    *   Ensure it is publicly accessible as `tokio_trace::span::Id`.
    *   Implement `Clone`, `Debug`, `PartialEq`, `Eq`, and `Hash`.

*   Implement `Span::child_of` method:
    *   Accept any type implementing `Into<Option<Id>>` as the parent argument.
    *   Signature: `pub fn child_of<I: Into<Option<Id>>>(parent: I, meta: &'a Metadata<'a>, values: &field::ValueSet) -> Span<'a>`.

*   Implement `Span::new_root` method:
    *   Create a new root span, independent of the current execution context.
    *   Signature: `pub fn new_root(meta: &'a Metadata<'a>, values: &field::ValueSet) -> Span<'a>`.

*   Implement `Into<Option<Id>> for &Span`:
    *   Allow a reference to a `Span` to be used as the parent argument in `Span::child_of` and the `span!` macro.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.