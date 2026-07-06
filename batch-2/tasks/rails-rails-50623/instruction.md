I'm working on Rails view rendering and I'd like renderable objects — the ones that implement a method to render themselves into a view context — to support receiving local variables and blocks from the caller.

*   When rendering a renderable object using the positional form (render obj, key: value), the named keyword arguments must be forwarded to the renderable's render_in method wrapped as a locals hash keyword argument: render_in receives locals: { key: value }.

*   When rendering a renderable object using the hash form (render renderable: obj, locals: hash), the locals hash must be passed to render_in as a locals: keyword argument; the renderable: key itself must NOT be passed to render_in.

*   When a block is given while rendering a renderable (either positional or hash form), the block must be forwarded to render_in. When the options hash contains a renderable: key, the block must be treated as belonging to the renderable renderer (not as a partial layout block).

*   If a renderable object's render_in method does not accept keyword arguments (old-style signature with only a single positional view_context parameter, with or without a block parameter), the framework must issue a deprecation warning via ActionView.deprecator; the deprecation message must contain the phrase 'without options'.

*   A NameError raised inside render_in must propagate to the caller without being masked or swallowed. The framework must rescue NameError (not only NoMethodError) and re-raise it when the object responds to render_in. The error message must include 'render_in' and the class name (e.g., 'NilClass').

*   The render method on a controller renderer must forward all arguments including blocks to the underlying render_to_string call. The renderer must support: render(obj, **locals), render(renderable: obj, locals: hash), render(obj) { block }, and render(renderable: obj) { block }.

*   A controller action using render renderable: obj, locals: hash must correctly pass the locals hash to the renderable and produce the expected response body; when no locals are provided, a default value must be used.

*   The ActionView::Template::Renderable class must accept a block at construction time and forward it to render_in. Its render(context, locals) method must call render_in(context, locals: locals, &stored_block) for renderables with the updated signature.

*   The internal render pipeline must propagate blocks through all layers: from the render call in the view helper down through the renderer, template renderer, template object, and finally to the render_in call on the renderable object.


*   Interface details: The changes required are enhancements to the existing render pipeline for renderable objects. No entirely new public classes or functions need to be created, but the following protocol and method behavior changes must be implemented:

---

## Renderable Protocol: render_in calling convention

**Protocol method:** `render_in`
**New required signature for renderable objects:** `render_in(view_context, **options, &block)`

The framework must call `render_in` with:
- `view_context` — the current view context (unchanged)
- `locals: hash` as a keyword argument — where `hash` is the locals hash provided by the caller. The locals hash is passed as a single keyword argument named `locals:`, NOT spread as individual keyword arguments.
- `&block` — the block passed at the render call site, if any

When `render(renderable: obj, locals: { a: 1, b: 2 })` is called, the renderable's `render_in` receives `render_in(ctx, locals: { a: 1, b: 2 })`. The `renderable:` key from the original options must NOT appear in the kwargs passed to `render_in`.

**Deprecation detection:** If a renderable object's `render_in` method does NOT accept keyword arguments (i.e., only has a single required positional parameter, with or without a block parameter), the framework must issue a deprecation via `ActionView.deprecator`. The deprecation message must contain the phrase `"without options"`.

---

## ActionView::Base#render — renderable-related behaviors

**Location:** `actionview/lib/action_view/helpers/rendering_helper.rb` or the underlying render implementation
**Existing method, enhanced behavior:**

- `render(renderable_object, **locals)` — positional form: locals are collected into a `locals:` kwarg and passed to `render_in`
- `render(renderable: obj, locals: { key: value })` — hash form: the `locals` value (a hash) is passed as a `locals: hash` keyword arg to `render_in`; the `renderable:` key must NOT appear in the kwargs sent to `render_in`
- `render(renderable_object) { block }` and `render(renderable: obj) { block }` — block is forwarded to `render_in`. When a `renderable:` key is present in the options hash, the block must be forwarded to the renderable renderer (not treated as a partial layout block)

---

## ActionController::Renderer#render — renderable-related behaviors

**Location:** `actionpack/lib/action_dispatch/routing/renderer.rb`
**Existing method, enhanced behavior:**

- `renderer.render(renderable_object, **locals)` — positional form with locals forwarded
- `renderer.render(renderable: obj, locals: hash)` — hash form with locals forwarded
- `renderer.render(renderable_object) { block }` and `renderer.render(renderable: obj) { block }` — with block forwarded
- The `render` method signature must forward all arguments including blocks (e.g., using `...` forwarding)

---

## ActionView::Template::Renderable — internal template class

**Location:** `actionview/lib/action_view/template/renderable.rb`
**Class:** `ActionView::Template::Renderable`

Updated behaviors:
- Constructor accepts a block: `initialize(renderable, &block)` — the block is stored and forwarded to `render_in`
- `render(context, locals)` method:
  - If `render_in` does not accept keyword arguments (old-style arity), call `@renderable.render_in(context, &@block)` and issue deprecation via `ActionView.deprecator` with message containing `"without options"`
  - Otherwise, call `@renderable.render_in(context, locals: locals, &@block)`
  - Must rescue `NameError` (not just `NoMethodError`) and re-raise if the object responds to `render_in`; only convert to `ArgumentError` if `render_in` is not defined at all

---

## TestRenderable#render_in (test support files only)

**Location (actionpack tests):** `actionpack/test/lib/test_renderable.rb`
**Location (actionview tests):** `actionview/test/lib/test_renderable.rb`
**Updated signature:** `render_in(view_context, **)`

Behavior:
- If a block is given: calls `view_context.render(html: yield)` and returns the result
- Otherwise: calls `view_context.render(inline: ERB_template, **)` where the ERB template outputs `Hello, <%= local_assigns[:name] || "World" %>!`
  - `**` passes the received locals (including the `locals:` key) to `view_context.render`, which makes them available via `local_assigns` inside the template

---

## Error propagation requirement

**NameError** raised inside `render_in` must propagate without being masked. Specifically, a `NameError` whose message contains "undefined method" must reach the caller with its original message intact, including both "render_in" and the class name on which the method was undefined (e.g., "NilClass"). Previously only `NoMethodError` was re-raised; now all `NameError` subtypes must be re-raised when the object responds to `render_in`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.