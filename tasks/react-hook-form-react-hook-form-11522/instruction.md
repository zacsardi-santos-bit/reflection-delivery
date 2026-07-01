Implement a standalone form control that allows external subscriptions to form state changes without causing React re-renders. Ensure that these subscriptions can be filtered by specific form state aspects and field names. Integrate this form control with React components, allowing them to selectively re-render based on explicitly subscribed state changes.

*   Implement `createFormControl` in `src/logic/createFormControl.ts`:
    *   Return an object containing a `subscribe` method and a `formControl` property.
    *   Ensure `formControl` references the full methods object, excluding itself, for use with `useForm`.

*   Implement the `subscribe` method:
    *   Accept an options object with `formState`, an optional `name`, and a `callback`.
    *   Return an `unsubscribe` function.
    *   Set the form's mount flag to true and merge provided `formState` keys into a separate proxy.
    *   Invoke the callback with the current form state snapshot and the triggering field's name when a matching state change occurs.

*   Ensure the `subscribe` method:
    *   Filters callback invocations by field name if a `name` filter is provided.
    *   Does not trigger React re-renders when used externally.

*   Update `useForm` in `src/useForm.ts`:
    *   Accept a `formControl` option to use an external form control.
    *   Ensure React components only re-render for explicitly subscribed formState properties.

*   Rename internal methods:
    *   `_updateValid` to `_setValid`, checking `_proxySubscribeFormState.isValid`.
    *   `_updateFieldArray` to `_setFieldArray`, checking additional proxy states.
    *   `_updateDisabledField` to `_setDisabledField`, causing an additional deferred render.
    *   `_executeSchema` to `_runSchema`.

*   Modify subject architecture:
    *   Consolidate value change notifications into `_subjects.state`.
    *   Ensure `useWatch` and watch callbacks subscribe to `_subjects.state`.

*   Ensure `useWatch` child components re-render twice after form submission.
*   Ensure `useController` triggers an additional render on blur events.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.