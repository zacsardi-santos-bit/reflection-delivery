## Description

The React code generator supports multiple state management strategies when compiling components. However, when MobX is selected as the state management approach, the generated output is incomplete and broken: it imports the observable state utility from the MobX library but fails to also import the observer wrapper needed to make reactive components work. It also exports the component directly as a regular function rather than wrapping it with the observer, which is required for MobX reactivity to function.

## Expected Behavior

- When the MobX state type is selected, the generated React code should import both the observable state hook **and** the observer wrapper from the MobX React library.
- The component function should be declared as a named (non-default-exported) function.
- After the component definition, the code should create a wrapped version of the component using the observer pattern and export that wrapped version as the default export.
- Existing output for other state types (e.g., standard React state) should be unaffected.

## Why This Matters

Developers who use MobX in their React projects need to generate code that works out of the box with MobX's reactive system. Currently, the generated code is missing the observer wrapping, meaning it would not actually react to state changes at runtime. This results in broken component behavior and forces developers to manually patch the generated output.
