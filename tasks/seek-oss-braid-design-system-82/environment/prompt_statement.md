I'm working on a component library and I've noticed two related issues. First, our Alert component requires an explicit tone to be provided — there's no way to render it in a neutral default state without specifying one. I'd like to make the tone optional so that when nothing is passed, the component still renders correctly with a default appearance.

Second, our test files are all plain JavaScript, which means we miss out on TypeScript type checking in tests. I'd like to convert the test files to TypeScript. Part of this includes our shared test utility that wraps tests in a theme context — it should be rewritten as a proper TypeScript module with typed callback signatures, and the theme and theme-provider types should be properly exported so they can be used in those typed utility functions.

Can you help me make the Alert component's tone optional and migrate the test infrastructure to TypeScript?
