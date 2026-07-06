Implement a built-in preset pattern for container queries in the framework's pattern system. This pattern should allow developers to easily declare elements as containers with optional name and type properties, defaulting to inline-size. Ensure the pattern works both as a function and a JSX component, and extend the at-rule sorting logic to handle container queries similarly to media queries.

*   Add a new pattern named 'cq' to `packages/preset-base/src/patterns.ts`:
    *   Accept properties 'name' (token type mapped to 'containerNames') and 'type' (default to 'inline-size').
    *   Map 'name' to 'containerName' and 'type' to 'containerType'.
*   Create a utility config in `packages/preset-base/src/utilities/container.ts`:
    *   Define 'containerName' with className 'cq-name' and values from 'containerNames'.
    *   Define 'containerType' with className 'cq-type'.
*   Ensure `cq({ name: 'sidebar' })` generates CSS classes:
    *   `.cq-type_inline-size { container-type: inline-size; }`
    *   `.cq-name_sidebar { container-name: sidebar; }`
*   Update `packages/types/src/tokens.ts`:
    *   Add 'containerNames: string' to the TokenDataTypes interface.
*   Extend `packages/types/src/theme.ts`:
    *   Add optional fields 'containerNames?: string[]' and 'containerSizes?: Record<string, string>'.
*   Generate artifact files for the 'cq' pattern:
    *   `patterns/cq.mjs`, `patterns/cq.d.ts`, `jsx/cq.mjs`, `jsx/cq.d.ts`.
*   Export functions in `patterns/cq.mjs`:
    *   `getCqStyle(styles?: CqStyles) => SystemStyleObject`
    *   `cq(styles?: CqStyles) => string` with `cq.raw` as `getCqStyle`.
*   Define TypeScript interfaces in `patterns/cq.d.ts`:
    *   `CqProperties` with optional 'name' and 'type'.
    *   `CqStyles` extending `CqProperties`.
    *   `CqPatternFn` for the callable pattern function.
    *   Export `declare const cq: CqPatternFn`.
*   Implement JSX component `Cq` in `jsx/cq.mjs` and `jsx/cq.d.ts`:
    *   Parseable as type "jsx-pattern".
*   Update property type definitions to include 'containerName':
    *   In non-strict mode: `Tokens["containerNames"] | CssProperties["containerName"]`.
    *   In strict mode: `Tokens["containerNames"]` only.
*   Modify `packages/core/src/plugins/sort-mq.ts`:
    *   Extend to sort `@container` at-rules like `@media` rules.
    *   Sort order: non-at-rule styles, then `@container` and `@media` with 'min-width', then 'max-width'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.