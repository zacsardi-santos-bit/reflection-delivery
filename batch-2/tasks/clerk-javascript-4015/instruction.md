Implement the necessary changes to the UI package's appearance customization system to address accessibility and mutation issues. Ensure the default appearance configuration is publicly accessible and that style overrides do not mutate the original theme.

*   Export the `defaultAppearance` constant from `packages/ui/src/contexts/AppearanceContext.tsx` as a named export.
    *   Ensure it has the shape `{ layout: object, elements: Record<string, { descriptor: string, className: string, style: {} }>, theme: object }`.
*   Update the `useAppearance()` hook:
    *   Return `{ parsedAppearance: { layout: defaultAppearance.layout, elements: defaultAppearance.elements, theme: defaultAppearance.theme }, theme: undefined, themelessAppearance: null }` when called within an `AppearanceProvider` without an `appearance` prop.
    *   When element overrides are provided as class strings, ensure `parsedAppearance.elements` entries have the format `{ descriptor: 'cl-{elementKey}', className: [defaultAppearance.elements[elementKey].className, 'user-class'].join(' '), style: {} }`.
*   Ensure `AppearanceProvider` behavior:
    *   When a single provider with element overrides is used, set `themelessAppearance` to `{ elements: { [elementKey]: userProvidedClass } }` without a `layout` key.
    *   When multiple nested providers specify classes for the same element, accumulate `parsedAppearance.elements[key].className` with all user-provided classes in outer-to-inner order, appended after the default class.
    *   In nested scenarios, `themelessAppearance.elements` must join user-provided classes as a space-separated string and include a `layout: {}` field.
*   Ensure style overrides do not mutate the original theme:
    *   Operate on an independent deep copy of the theme for each application of appearance customization.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.