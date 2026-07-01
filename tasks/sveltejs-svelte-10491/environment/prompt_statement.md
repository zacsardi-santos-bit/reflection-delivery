I've been noticing that the Svelte compiler is incorrectly adding the component scoping class to CSS selectors. There seem to be two opposite problems happening at once.

First, when I write selectors using direct-child or sibling operators, the element on the left side of the operator is being given the scoping attribute even though it doesn't need it. The operator itself already constrains the rule to the right structural relationship, so adding scoping on both sides is wrong and produces incorrect output.

Second, when I write selectors using the descendant relationship (just a space between elements), only the very last element ends up scoped. The intermediate elements in the chain don't get the scoping attribute, which means the styles could potentially match elements from outside the component that happen to be descendants of the outermost scoped element. These intermediate elements should be scoped too, and that should be reflected in the HTML output as well.

I also expect that a selector whose full descendant chain doesn't match any element in the template should be identified as unused. And chains of globally-declared selectors that precede a local element should work without triggering false "unused selector" warnings.

Additionally, I'd like native CSS nesting to be properly supported, so that styles written inside other style rules are correctly scoped, with unused or empty nested rules annotated as comments in the output.

Finally, CSS attribute selectors that contain special characters in their values are not being parsed correctly — the output gets corrupted.
