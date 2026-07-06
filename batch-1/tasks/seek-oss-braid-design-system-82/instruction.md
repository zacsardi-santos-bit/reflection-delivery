Update the Alert component to make the tone prop optional, ensuring it renders with a default appearance when no tone is specified. Convert all test files to TypeScript and ensure type safety by exporting necessary types and updating test utilities.

*   Modify the Alert component:
    *   Make the tone prop optional in the Alert component.
    *   Implement a default appearance for the Alert when no tone is provided.
    *   Export the AlertProps type from `lib/components/Alert/Alert.tsx` with the tone property as optional.

*   Update TypeScript type exports:
    *   Export the ThemeProviderProps type from `lib/components/ThemeProvider/ThemeProvider.tsx`.
    *   Export the Theme type from `lib/themes/theme.ts`.

*   Refactor test utilities and files:
    *   Rewrite the `forEachTheme` utility as a TypeScript module in `lib/test/utils/forEachTheme.tsx`.
        *   Ensure it accepts a typed callback with parameters: theme (of type Theme) and ThemeProvider (a React function component with the theme prop omitted).
    *   Convert all component test files to TypeScript (.tsx) and ensure they compile without type errors.
    *   Ensure any noop functions in tests explicitly return undefined to comply with strict TypeScript type checking.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.