## Description

Currently, there is no lint rule to detect when accessibility attributes are applied to HTML elements that don't actually support those attributes based on the element's implied role. Developers can unknowingly write semantically incorrect accessible markup — for example, marking a link as "checked" or "invalid", marking a plain list as "expanded", or using state attributes on input types that don't have those states in their ARIA role definition. These mistakes silently produce invalid accessibility markup that may confuse assistive technologies.

## Expected Behavior

A new lint rule should be added that:
- Detects when an ARIA attribute is used on a JSX/HTML element whose implicit role (determined by the element tag and relevant attributes like input type or the presence of href) does not support that attribute
- Reports a clear diagnostic indicating which ARIA attribute is unsupported on the given element
- Includes guidance telling developers to ensure their ARIA attributes are valid for the element's role
- Does NOT flag custom components, elements with spread attributes, or elements with explicit presentation roles, since their effective role cannot be statically determined
- Does NOT flag elements when the ARIA attribute is genuinely supported by the element's role

## Why This Matters

Writing accessibility attributes that don't apply to an element's role is a common mistake that produces confusing or incorrect markup for assistive technology users. Catching these errors statically during development helps teams ship accessible interfaces with fewer bugs and avoids subtle issues that won't be caught by visual testing.
