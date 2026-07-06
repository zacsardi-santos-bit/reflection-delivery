I'm doing some work on Rails view rendering and I want renderable objects, you know the ones that implement a `render_in` method to draw themselves into a view context, to be able to take local variables and blocks from whoever's rendering them. Right now there's no clean way to pass data to a renderable inline, so components end up leaning on instance variables and other hacks instead of just being parameterized properly.

What I'm after: when I render a renderable I want to pass named locals directly and have them show up inside the renderable's `render_in` as keyword arguments. This should work both positional style (passing the renderable object straight to render) and hash style (a named option key pointing at the renderable plus a separate `locals:` key). And I want to hand it a block too so the component can yield to it.

One gotcha with the hash style, don't forward the framework's own internal option keys (like the one naming the renderable itself) into `render_in`, only the actual locals should pass through.

For backwards compat, renderables whose `render_in` still uses the old signature without keyword support should get a deprecation warning instead of blowing up right away. And any error raised inside `render_in`, including a NoMethodError from calling something that doesn't exist on an object, needs to surface to the caller with its full message intact and not get swallowed by the framework.

Oh and this all needs to work through the standalone renderer controllers expose for out-of-request rendering too, both positional and hash-style APIs, with or without a block. The relevant bits live around the rendering code in `@actionview/lib/action_view/renderer/renderable_renderer.rb` and the controller renderer path, so check both.
