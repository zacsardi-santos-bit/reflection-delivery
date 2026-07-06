Fix the Mitosis-to-Angular code generator to ensure correct initialization order for state variables derived from props. Implement the necessary changes to ensure that state initialization from props occurs before any dependent computed bindings in the Angular lifecycle.

*   Modify the Angular generator to ensure state initialization from props occurs first in `ngOnInit()`:
    *   Ensure `this.val = this.value` is executed before `this.node_0_Comp = { ...this.val }` in the `ngOnInit()` method when using class property bindings.
    *   Ensure `ngOnChanges()` updates `this.node_0_Comp` with `{ ...this.val }` only if `typeof window !== 'undefined'`.

*   In standard Angular mode:
    *   Render the spread binding as `[val]="useObjectWrapper(val)"` in the template.
    *   Implement a `useObjectWrapper(...args)` helper method on the class to merge arguments into a single object.
    *   Ensure `ngOnInit()` contains only `this.val = this.value`.

*   Update the state transformation function in `packages/core/src/generators/angular/index.ts`:
    *   Prepend prop-dependent state initialization statements to the beginning of `ngOnInit` code.
    *   Process entries in reverse order to maintain the original state declaration order in `ngOnInit`.

*   Create a new test fixture file at `packages/core/src/__tests__/data/angular/state-init-sequence.raw.tsx`:
    *   Include a Mitosis component that initializes state from a prop and uses that state in a spread binding to a child component.

*   Register the `stateInitSequence` key in the `ANGULAR_TESTS` object in `packages/core/src/__tests__/test-generator.ts`:
    *   Point it to the new fixture file using `getRawFile('./data/angular/state-init-sequence.raw.tsx')`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.