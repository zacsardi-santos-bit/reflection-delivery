I'm running into multiple issues with how Svelte scopes CSS selectors in components. A few specific problems I've hit:

When I write a style rule that targets an element through multiple levels of ancestor context (like styling an element that's a descendant of a descendant), intermediate elements are missing their scoping attributes in the compiled output. The generated selector doesn't match the right elements. Meanwhile, when I use a child combinator or a sibling combinator, the element appearing before the combinator is getting an unwanted scoping modifier that it shouldn't have — only the final target should be scoped in those cases.

I also tried using the selector-grouping pseudo-class in my styles, and the output doesn't scope any of the alternatives inside it, nor does it mark the unused ones. I'd expect matching selectors inside the pseudo-class to be scoped and non-matching ones to be commented out.

Native CSS nesting doesn't work at all right now. If I write a rule with nested child rules (using either the implicit parent-reference style or the explicit nesting selector), the compiler doesn't understand the nesting relationship and produces broken scoped output. I'd like to be able to write modern nested CSS in my component style blocks.

Additionally, selectors that could never match because a required intermediate element simply doesn't exist in the component's template should be marked as unused, and attribute selectors with special characters in their values should parse without errors.

Can you fix the CSS scoping logic to handle all of these cases correctly?
