Update the user store to use the new standalone function pattern for authentication operations, ensuring the auth instance is passed as the first argument. Centralize error messages using the shared message dictionary.

*   Modify the `UserStore` class in `src/stores/User/user.store.ts`:
    *   Implement the `login(email: string, password: string) -> Promise<any>` method to call `signInWithEmailAndPassword` as a standalone function, passing the `auth` instance, `email`, and `password` as arguments.
    *   Implement the `registerNewUser(displayName: string, email: string, password: string) -> Promise<void>` method to:
        *   Use `createUserWithEmailAndPassword` as a standalone function, passing the `auth` instance, `email`, and `password`.
        *   Update the current user's profile using `updateProfile` as a standalone function, passing `auth.currentUser` and the profile update object.

*   Ensure the `auth` instance in `UserStore` is obtained by calling `getAuth` from the `firebase/auth` module.
    *   The test mock provides `getAuth` returning an object with a `currentUser` property (`displayName: 'testDisplayName', uid: 'testUid'`).

*   Use the `FRIENDLY_MESSAGES` mapping from `shared/messages.ts`:
    *   Include the key `'auth/email-already-in-use'` with the value `'The email address is already in use'`.
    *   Ensure error messages in the sign-up process are sourced from this mapping rather than hardcoded.

*   Update the `src/utils/firebase.ts` module:
    *   Export an `auth` object that is a Firebase v9 modular Auth instance obtained via `getAuth`.
    *   Ensure compatibility with standalone `firebase/auth` functions that accept the `auth` instance as their first argument.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.