Implement the necessary changes to fix two bugs in Storybook's knobs addon and hooks system. Ensure knob options are updated on re-render and side effects execute only once per render cycle.

*   Update the `KnobStore` class:
    *   Implement the `update(key: string, options: Partial<KnobStoreKnob>): void` method in `addons/knobs/src/KnobStore.ts`.
        *   Merge provided partial options into the existing knob stored at the specified key.
        *   Preserve any existing knob fields not present in the new options.
*   Modify the knob registration process:
    *   When a knob is registered with a name that already exists in the store and has a matching type, invoke `knobStore.update(knobName, restOptions)`.
        *   Ensure `restOptions` includes all configuration options except the value property.
        *   Reflect the latest configuration in the knobs panel by including additional metadata fields.
*   Adjust the `useEffect` function:
    *   Modify the function in `lib/client-api/src/hooks.ts` to deduplicate effects within a single render cycle.
        *   Ensure the effect callback is only invoked once per cycle, even if the story function is called multiple times by a decorator.
        *   Track effect objects to prevent adding the same effect more than once in a single render cycle.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.