I'm hitting a bundler issue with CommonJS interop. I have a CommonJS module that conditionally re-exports one of two other CommonJS modules at runtime — it picks between them based on a condition evaluated at runtime. When an ESM file imports this module as a default import and calls it as a function, the bundler generates incorrect output.

The generated bundle is missing the required interop compatibility step. Instead of going through the interop layer to reach the actual exported function, the generated code tries to use the wrapper object directly. At runtime this fails because the actual function value lives under an additional property in the interop wrapper, not at the top level.

The same problem affects named property access: when an ESM file accesses a named property on a namespace binding that came from a CommonJS module via interop, the bundler sometimes skips the interop compatibility accessor and accesses the property directly on the wrapper, returning the wrong value.

The fix should ensure that when a CommonJS module uses a conditional require expression as its export, the bundler correctly recognizes this pattern and does not apply the optimization that eliminates the interop compatibility accessor. The bundled output must always go through the proper interop layer when accessing the default export or any named property from such a module.
