I'm working with webpack's internal HTML and CSS parsing utilities and I'd like to consolidate them into cleaner entry-point modules.

*   A new module at lib/html/syntax must exist and export all HTML parsing utilities as named exports, consolidating what was previously split between lib/html/buildHtmlAst and lib/html/walkHtmlTokens.

*   lib/html/syntax must export the buildHtmlAst function as a named export (not a default export), so it can be individually replaced in module mocks while all other exports remain accessible via the real module.

*   lib/html/syntax must export the namespace constants NS_HTML, NS_MATHML, and NS_SVG as named exports.

*   lib/html/syntax must export the walkHtmlTokens function and the quote-type constants QUOTE_DOUBLE, QUOTE_SINGLE, and QUOTE_NONE as named exports.

*   lib/html/syntax must export the decodeHtmlEntities and decodeHtmlEntitiesWithMap functions as named exports.

*   lib/html/syntax must provide TypeScript type definitions for HtmlNode, HtmlElement, HtmlText, HtmlComment, HtmlDoctype, and HtmlDocument.

*   A new module at lib/css/syntax must exist and export all CSS parsing utilities as named exports, consolidating what was previously in lib/css/walkCssTokens.

*   lib/css/syntax must export the functions escapeIdentifier, unescapeIdentifier, and equalsLowerCase as named exports.

*   lib/css/syntax must export the functions readToken, parseAListOfComponentValues, parseAStylesheet, and parseAStylesheetsContents as named exports.

*   lib/css/syntax must export the token type constants TT_WHITESPACE, TT_RIGHT_CURLY_BRACKET, and TT_SEMICOLON as named exports.

*   lib/css/syntax must provide TypeScript type definitions for MutableToken, ComponentValue, NumberToken, PercentageToken, DimensionToken, HashToken, UrlToken, FunctionNode, Declaration, QualifiedRule, AtRule, Rule, Node, Token, VisitorMap, and VisitorContext.


*   Interface details: Type: Module
Name: lib/html/syntax
Location: lib/html/syntax.js
Description: Unified entry point for all HTML parsing utilities. Must export all of the following as named exports.

Named exports required:
- buildHtmlAst — the HTML AST builder function (must be a named export, NOT the module's default export, so it can be individually mocked while other exports remain via requireActual)
- NS_HTML — namespace constant string for HTML
- NS_MATHML — namespace constant string for MathML
- NS_SVG — namespace constant string for SVG
- walkHtmlTokens — the HTML tokenizer function
- QUOTE_DOUBLE — quote-type constant for double-quoted attributes
- QUOTE_SINGLE — quote-type constant for single-quoted attributes
- QUOTE_NONE — quote-type constant for unquoted attributes
- decodeHtmlEntities — function to decode HTML entities from a string
- decodeHtmlEntitiesWithMap — function to decode HTML entities and return a source-position map

TypeScript type definitions that must be accessible via import("lib/html/syntax"):
- HtmlDocument
- HtmlNode
- HtmlElement
- HtmlText
- HtmlComment
- HtmlDoctype

---

Type: Module
Name: lib/css/syntax
Location: lib/css/syntax.js
Description: Unified entry point for all CSS parsing utilities. Must export all of the following as named exports.

Named exports required:
- readToken — the low-level CSS token reader function
- parseAListOfComponentValues — CSS component value list parser
- parseAStylesheet — CSS stylesheet parser
- parseAStylesheetsContents — CSS stylesheet contents parser
- escapeIdentifier — CSS identifier escape function
- unescapeIdentifier — CSS identifier unescape function
- equalsLowerCase — CSS case-insensitive equality helper
- TT_WHITESPACE — token type constant for whitespace
- TT_RIGHT_CURLY_BRACKET — token type constant for right curly bracket
- TT_SEMICOLON — token type constant for semicolon

TypeScript type definitions that must be accessible via import("lib/css/syntax"):
- MutableToken
- ComponentValue
- NumberToken
- PercentageToken
- DimensionToken
- HashToken
- UrlToken
- FunctionNode
- Declaration
- QualifiedRule
- AtRule
- Rule
- Node
- Token
- VisitorMap
- VisitorContext


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.