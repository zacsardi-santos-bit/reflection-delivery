I'm running into a crash when building authentication tokens for users whose stored documents are missing some field data.

*   The getFieldsToSign function must not throw an error when the user object is missing data for group-type or tab-type fields defined in the collection configuration (e.g., when user[groupFieldName] or user[tabFieldName] is undefined).

*   When getFieldsToSign is called with a user object that is missing group or tab field data, it must return an object that includes at minimum: the user's id (matching the user's id property), the email (matching the email parameter), and the collection slug (from the collectionConfig).

*   The getFieldsToSign function must be exported from the 'payload' package so it can be imported and used directly in integration code.


*   Interface details: Type: Function
Name: getFieldsToSign
Location: packages/payload/src/auth/getFieldsToSign.ts
Signature: getFieldsToSign({ collectionConfig, email, user }: { collectionConfig: SanitizedCollectionConfig, email: string, user: Record<string, unknown> }) -> Record<string, unknown>
Description: Builds the set of fields to include in a JWT for a given user. Must handle missing group-type and tab-type field data gracefully (treating undefined data as an empty object instead of crashing). Returns an object containing at minimum: id (from user), email (from the email parameter), and collection (the collection's slug). This function must be exported from the top-level 'payload' package.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.