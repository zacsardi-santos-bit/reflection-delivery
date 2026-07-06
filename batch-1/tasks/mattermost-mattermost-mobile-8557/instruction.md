Implement support for custom profile attributes in the mobile app's edit-profile screen. Ensure that these attributes are fetched, displayed, and updated correctly when enabled on the server. Introduce necessary functions, methods, and components to handle dynamic custom attribute fields.

*   Implement `fetchCustomAttributes` in `app/actions/remote/user.ts`:
    *   Accept `serverUrl` and `userId` as parameters.
    *   Fetch attribute field definitions and current values in parallel.
    *   Return a result object with an `attributes` property mapping field IDs to objects with `id`, `name`, and `value` fields.
    *   Ensure an empty `attributes` object is returned when no fields exist.
    *   Include a truthy `error` property if fetching fails.

*   Implement `updateCustomAttributes` in `app/actions/remote/user.ts`:
    *   Accept `serverUrl` and `attributes` (CustomAttributeSet) as parameters.
    *   Transform the attribute set into a `{fieldId: value}` mapping.
    *   Call the REST client's `updateCustomProfileAttributeValues` method with this mapping.
    *   Return `{success: true, error: undefined}` on success or `{success: false, error: <error>}` on failure.

*   Update the REST client in `app/client/rest/custom_profile_attributes.ts`:
    *   Implement `updateCustomProfileAttributeValues` to make a PATCH request to `${getCustomProfileAttributesRoute()}/values` with the values object.

*   Create `useFieldRefs` hook in `app/hooks/field_refs.ts`:
    *   Return a tuple `[getRef, setRef]`.
    *   Implement `getRef(key)` to return the stored ref or undefined.
    *   Implement `setRef(key)` as a curried function that stores a ref and returns a cleanup function to remove it.

*   Update `ProfileForm` component in `app/screens/edit_profile/components/form.tsx`:
    *   Accept an optional `enableCustomAttributes` boolean prop.
    *   Conditionally render custom attribute input fields based on `enableCustomAttributes` and `userInfo.customAttributes`.
    *   Assign container testID `edit_profile_form.customAttributes.{fieldId}` and input testID `edit_profile_form.customAttributes.{fieldId}.input`.
    *   Call `onUpdateField("customAttributes.{fieldId}", newValue)` when input changes.

*   Update `UserInfo` interface in `types/screens/edit_profile.ts`:
    *   Include `customAttributes` field of type `CustomAttributeSet`.

*   Define `CustomAttributeSet` and `CustomAttribute` in `types/screens/edit_profile.ts`:
    *   `CustomAttributeSet`: Map from field ID strings to `CustomAttribute` objects.
    *   `CustomAttribute`: Contains `id`, `name`, and `value` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.