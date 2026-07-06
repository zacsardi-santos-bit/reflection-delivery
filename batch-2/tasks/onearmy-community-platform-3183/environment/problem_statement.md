## Description

Our app uses a cloud authentication service, and we need to upgrade how we interact with it. Currently, authentication operations are invoked as methods on a shared auth object. The newer version of the library requires these operations to be standalone functions that accept the auth instance as their first parameter rather than being called as methods on that instance.

This means that the user store's login and registration flows need to be updated to follow the standalone function calling pattern instead of the object-method pattern.

## Expected Behavior

- The user login flow should call the sign-in function as a standalone imported function, with the auth instance passed as the first argument followed by email and password
- The user registration flow should similarly use standalone imported functions from the auth module for creating users and updating user profiles
- The auth instance itself must be retrieved using the modular getter function from the auth library
- Error messages displayed on sign-up failures should be sourced from the shared message dictionary rather than being hardcoded in the component. Specifically, the message for when a user tries to sign up with an email address that is already in use must be defined in the shared messages mapping

## Why This Matters

The authentication library has moved away from the older object-method API style. Without this update, the app and its tests fail because the mocked authentication module now targets standalone functions rather than methods on a shared object. Centralizing error messages in a shared dictionary also ensures consistency and easier future maintenance.
