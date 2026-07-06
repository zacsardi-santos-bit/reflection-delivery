I'm building a UI component library and I need a set of helper utilities and a React hook that make it easier to write consistent, well-behaved components from a shared declarative configuration.

*   The resolveResponsiveValue function must return primitive values (strings, numbers, undefined, null) and plain non-responsive objects unchanged. A plain object is considered non-responsive if it contains none of the standard breakpoint keys (xs, sm, md, lg, xl) — an object with only an 'initial' key is therefore returned unchanged.

*   When resolveResponsiveValue receives a responsive object (one containing at least one of xs, sm, md, lg, xl), it must return the value at the exact requested breakpoint if available, or fall back to the nearest smaller defined breakpoint in order [initial, xs, sm, md, lg, xl]. If no value exists at or below the requested breakpoint, it must fall forward to the smallest defined named breakpoint. Undefined values at a breakpoint are skipped during fallback traversal.

*   The resolveDefinitionProps function must return { ownPropsResolved, restProps } where ownPropsResolved contains only props whose keys appear in definition.propDefs (with responsive values resolved at the given breakpoint and defaults applied), and restProps contains only props whose keys are neither in propDefs nor in definition.utilityProps.

*   resolveDefinitionProps must apply default values from propDefs[key].default when a prop is absent. It must preserve explicitly provided falsy values (false, 0, empty string) over the default. Props that resolve to undefined with no default must be omitted from ownPropsResolved (the key must not be present).

*   resolveDefinitionProps must not pass utility props (listed in definition.utilityProps) into restProps. A utility prop that also appears in propDefs must appear in ownPropsResolved. Rest props are passed through as-is without responsive resolution.

*   The processUtilityProps function must return { utilityClasses: '', utilityStyle: {} } when all provided utility values are undefined, null, or absent.

*   processUtilityProps must generate class names in the form 'bui-{propShorthand}-{value}' for predefined discrete values (e.g., m='2' produces 'bui-m-2', position='relative' produces 'bui-position-relative'). Invalid values for fixed-value props must be silently ignored (produce no class and no style entry).

*   processUtilityProps must generate both a class name 'bui-{propShorthand}' and a CSS custom property '--{propName}: value' for custom/arbitrary values (e.g., width='100px' produces class 'bui-w' and style { '--width': '100px' }).

*   processUtilityProps must handle responsive utility values: the 'initial' breakpoint produces no prefix (e.g., 'bui-m-2'), other breakpoints are prefixed as '{breakpoint}:bui-{shorthand}-{value}' (e.g., 'md:bui-m-4'). Multiple props must be combined into one space-separated utilityClasses string and one merged utilityStyle object.

*   processUtilityProps must apply transform functions for specific props: for 'grow', boolean true must become the number 1 (output { '--grow': 1 }); for 'basis', a number must be converted to a px string (e.g., 42 → '--basis': '42px').

*   The useDefinition hook must return { ownProps, restProps, dataAttributes, utilityStyle } where ownProps includes a 'classes' object whose keys match definition.classNames and whose values are class strings beginning with the corresponding definition.styles entry. User-provided className is appended to the slot named by options.classNameTarget (defaults to 'root'; passing null disables all className appending). Utility classes are appended to the slot named by options.utilityTarget (defaults to 'root'; passing null disables all utility class appending). Non-targeted slots must not contain utility classes or user className.

*   useDefinition must generate 'data-{propName}' entries in dataAttributes for each propDef with dataAttribute: true whose resolved value is not undefined. Non-string values must be stringified. When definition.bg is 'provider', data-bg must be set via the bg-provider resolution path (not the normal propDef dataAttribute path).

*   When definition.bg is 'provider', ownProps must include childrenWithBgProvider: children wrapped in the background context provider using the resolved bg value. For neutral bg, the level must be incremented relative to the parent background context (e.g., parent neutral-1 → resolved as neutral-2). When bg is undefined, childrenWithBgProvider must equal the raw children unchanged. No data-bg must be set when bg prop is undefined.

*   When definition.bg is 'consumer', useDefinition must set data-on-bg in dataAttributes from the parent background context value (omit when no parent context exists). ownProps must include children but must NOT include childrenWithBgProvider. When definition has no bg config, neither data-bg nor data-on-bg must appear, and ownProps must include children.

*   When definition.analytics is true, useDefinition must include an analytics object in the result with a captureEvent method. When the noTrack prop is true, the analytics value must be noopTracker (imported from the analytics module). When definition.analytics is not set, the analytics key must not appear in the result.

*   useDefinition must resolve href values inside a router context: relative paths (e.g., 'foo', './foo', '../foo', '') are resolved against the current route; absolute paths starting with '/' pass through unchanged; external URLs (https://, http://, mailto:, tel:, //) pass through unchanged. When href is undefined, the href key must not appear in ownProps. Outside a router context, all href values pass through unchanged.

*   A test setup file must be created at packages/ui/src/setupTests.ts that imports @testing-library/jest-dom so that DOM matchers such as toHaveTextContent are available in the test environment. The @testing-library/react and @testing-library/jest-dom packages must be added as devDependencies in packages/ui/package.json.


*   Interface details: Type: Function
Name: resolveResponsiveValue
Location: packages/ui/src/hooks/useDefinition/helpers.ts
Signature: resolveResponsiveValue(value: any, breakpoint: string): any
Description: Resolves a potentially responsive value to a concrete value at the given breakpoint. Returns non-object values (strings, numbers, undefined, null) unchanged. Returns plain objects that do not contain standard breakpoint keys (xs, sm, md, lg, xl) unchanged — including objects with only an "initial" key. For responsive objects (objects containing at least one of xs, sm, md, lg, xl): returns the exact match for the given breakpoint if available; falls back to the nearest smaller defined breakpoint value; falls back to the "initial" key value if no named breakpoint at or below the current one is defined; falls forward to the smallest available named breakpoint if nothing at or below current exists (e.g., when breakpoint is "initial"); skips undefined values during fallback traversal.

Type: Function
Name: resolveDefinitionProps
Location: packages/ui/src/hooks/useDefinition/helpers.ts
Signature: resolveDefinitionProps(definition: ComponentConfig<any, any>, props: Record<string, any>, breakpoint: string): { ownPropsResolved: Record<string, any>, restProps: Record<string, any> }
Description: Separates incoming props into own props (those declared in definition.propDefs) and rest props (everything else). Utility props listed in definition.utilityProps are excluded from restProps but are only included in ownPropsResolved if they also appear in propDefs. Default values from propDefs[key].default are applied when a prop is not explicitly provided; falsy values (false, 0, empty string) take precedence over defaults. Responsive values for own props are resolved at the given breakpoint via resolveResponsiveValue. Own props that are undefined after resolution and have no default are omitted from ownPropsResolved. Rest props are passed through as-is without responsive resolution.

Type: Function
Name: processUtilityProps
Location: packages/ui/src/hooks/useDefinition/helpers.ts
Signature: processUtilityProps(props: Record<string, any>, utilityPropNames: readonly string[]): { utilityClasses: string, utilityStyle: Record<string, any> }
Description: Processes utility prop values into CSS class names and CSS custom properties. Returns { utilityClasses: '', utilityStyle: {} } when no utility props have values (undefined or null values are skipped). For predefined discrete values (e.g., spacing tokens), generates a class name of the form "bui-{propShorthand}-{value}" (e.g., m='2' → 'bui-m-2', position='relative' → 'bui-position-relative'). Invalid values for fixed-value props are silently ignored. For custom/arbitrary values (e.g., width='100px'), generates both a class name "bui-{propShorthand}" (e.g., 'bui-w') and a CSS custom property "--{propName}: value" (e.g., '--width': '100px'). Responsive values are expanded per breakpoint: "initial" uses no prefix (e.g., 'bui-m-2'), other breakpoints use the prefix format "{breakpoint}:bui-{shorthand}-{value}" (e.g., 'md:bui-m-4'). Transform functions are applied before output for specific props: grow converts boolean true to the number 1; basis converts a number to a px string (e.g., 42 → '42px'). Multiple props are combined: utilityClasses is a space-separated string, utilityStyle merges all CSS vars.

Type: Function
Name: useDefinition
Location: packages/ui/src/hooks/useDefinition/useDefinition.ts
Signature: useDefinition(definition: ComponentConfig<any, any>, props: Record<string, any>, options?: { classNameTarget?: string | null, utilityTarget?: string | null }): { ownProps: Record<string, any>, restProps: Record<string, any>, dataAttributes: Record<string, string>, utilityStyle: Record<string, any>, analytics?: object }
Description: React hook that processes a component definition and raw props into resolved prop sets, CSS class maps, data attributes, utility styles, and optional analytics. Returns an object with: ownProps (resolved own props plus a "classes" object mapping slot names to class strings), restProps (passthrough HTML attributes), dataAttributes (data-* attribute map), utilityStyle (CSS custom properties), and optionally analytics. The "classes" object keys match definition.classNames keys; each slot's value starts with the base CSS class from definition.styles. User-provided className is appended to the slot named by options.classNameTarget (defaults to 'root'; null disables it). Utility classes are appended to the slot named by options.utilityTarget (defaults to 'root'; null disables it). Non-targeted slots remain clean. Data attributes: generates 'data-{propName}' for each prop in propDefs with dataAttribute: true if the resolved value is not undefined; non-string values are stringified. When definition.bg is 'provider': data-bg is set from the resolved bg prop value (not the normal dataAttribute path); ownProps includes childrenWithBgProvider (children wrapped in a background context provider when bg is set, or the raw children when bg is undefined); the neutral bg level is incremented relative to parent context (e.g., parent neutral-1 → child uses neutral-2). When definition.bg is 'consumer': data-on-bg is set from the parent background context value (omitted if no parent context); ownProps includes children but NOT childrenWithBgProvider. When no bg config: no data-bg or data-on-bg; ownProps includes children. When definition.analytics is true: result includes an analytics object with a captureEvent method; when the noTrack prop is true, returns noopTracker as the analytics value; when definition.analytics is not set, no analytics key is present. Href resolution: inside a router context, relative hrefs are resolved against the current route (e.g., 'foo' from '/catalog' → '/catalog/foo', '../foo' from '/catalog/items' → '/catalog/foo', '' → current route); absolute paths starting with '/' pass through; external URLs (https://, http://, mailto:, tel:, //) pass through unchanged; when href is undefined, it is omitted from ownProps. Outside a router context, all href values pass through unchanged.

Type: Interface
Name: ComponentConfig
Location: packages/ui/src/hooks/useDefinition/types.ts
Description: Configuration object describing a UI component's prop declarations, styles, and behaviors. Shape: { styles: Record<string, string>, classNames: Record<string, string>, propDefs: Record<string, { dataAttribute?: boolean, default?: any }>, utilityProps?: readonly string[], bg?: 'provider' | 'consumer', analytics?: boolean }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.