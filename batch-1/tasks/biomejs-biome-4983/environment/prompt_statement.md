I'm working on Biome and want to add a new lint rule for Solid.js projects in the nursery group. The rule should prevent developers from destructuring props directly in the component's function parameter, since this breaks Solid's reactivity system.

In Solid, props need to be accessed through property lookups to preserve reactive tracking. When you destructure props at the parameter level — even with default values, aliased names, computed keys, rest elements, or TypeScript type annotations — the reactive signal is lost. This is a very common mistake especially for developers coming from other frameworks.

The rule should fire on arrow functions that look like Solid components: specifically, arrow functions with a single object-destructured parameter that are assigned to a PascalCase-named variable. It should detect all forms of object destructuring in that position, including empty destructuring, rest elements, default values, aliased properties, computed property keys, and TypeScript-typed destructuring. For each destructured variable that gets used as a JSX attribute value, the diagnostic should point to where the variable is used, with a secondary pointer back to where the destructuring was defined.

The rule should not fire on functions that have more than one parameter (those aren't Solid components), functions where props are passed as a plain parameter and accessed with property syntax, cases where destructuring happens inside the function body rather than the parameter, or nested inner functions inside a component.

The error messages should make clear that this is a Solid-specific concern about property access and reactivity, and should guide the developer to use property accesses on the props object instead.
