I'm working on the experimental icon package in a React Native design system library and I need to add a font icon component. The idea is that many icon sets are distributed as font files where each icon corresponds to a Unicode character at a specific numeric code point. I want a component that accepts a font family name, a numeric codepoint, a color, and an optional font size, and renders the matching character styled with those values.

The component should render as a simple text element with the character as its content — no extra wrappers. When a font size isn't provided, the size should still be represented as a field in the style (present but without a value, rather than being omitted from the style object entirely). The component also needs to be stable and consistent across multiple renders with the same props.

The component should be exported from the package's public surface so other parts of the app can import it by name.
