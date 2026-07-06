## Description

Template files don't currently support placing comments inside HTML element attribute lists. When developers want to explain why a particular attribute is set — for example, to document a browser quirk workaround or clarify the purpose of an attribute value — there is no way to do so inline. Adding a comment next to an attribute simply breaks the parser.

## Expected Behavior

- Developers should be able to place both single-line and block-style comments between attributes within an element's opening tag
- The parser should recognize these attribute comments and represent them in the parsed template structure, capturing the comment text and whether it is a single-line or block-style comment
- When the template is compiled and rendered, all attribute comments must be stripped — they should produce no output in the final HTML
- Attribute comments may appear alongside any attribute type (constant attributes, expression attributes, boolean attributes, etc.)

## Why This Matters

Large templates often contain subtle attribute decisions that aren't obvious from reading the attribute name and value alone. Without inline comments, developers have to rely on external documentation or separate code comments far from the relevant attribute. Supporting comments directly inside attribute lists makes templates self-documenting, especially for attributes that encode non-obvious business rules or browser compatibility workarounds.
