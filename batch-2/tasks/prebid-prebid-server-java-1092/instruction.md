Implement a method to handle impression-level first-party data (FPD) for auctions, ensuring data is correctly merged or stripped based on bidder permissions. Update the Rubicon adapter to prioritize ad unit code selection from impression data sources.

*   Implement the `resolveImpExt` method in `FpdResolver` class with the signature:
    *   `public ObjectNode resolveImpExt(ObjectNode originalImpExt, ObjectNode updatedImpExt, boolean useFirstPartyData)`
    *   Modify `updatedImpExt` in-place and return it.
*   Handle context and data fields based on `useFirstPartyData`:
    *   If `originalImpExt` lacks a 'context' field, do not set 'context' in the result.
    *   If `useFirstPartyData` is true:
        *   Set a deep copy of `originalImpExt.context` if it is an `ObjectNode`.
        *   Merge `context.data` with top-level `data` if both are `ObjectNodes`.
        *   Set `context.data` as `data` if top-level `data` is absent.
        *   Set `data` unchanged if it is not an `ObjectNode`.
    *   If `useFirstPartyData` is false:
        *   Remove 'data' from `context` and omit 'context' if empty.
        *   Set `context` unchanged if it is not an `ObjectNode`.
        *   Do not set a top-level 'data' field.
*   Update `ExchangeService` to delegate impression extension preparation to `resolveImpExt`, removing previous inline logic.
*   Modify `OrtbTypesResolver.normalizeBidRequest` to skip user data normalization if the data field is not an `ObjectNode`.
*   Update `Rubicon` adapter's `makeHttpRequests` to prioritize ad unit code selection:
    *   Check `imp.ext.context.data.adserver` and `imp.ext.data.adserver` for GAM adserver slots.
    *   Fallback to `pbadslot` from `imp.ext.context.data` and `imp.ext.data`.
    *   Ignore non-GAM adserver entries for ad unit code.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.