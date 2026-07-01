## Description

There are several issues with the user management system that need to be addressed:

1. **Profile images are never returned correctly.** When retrieving a user's profile image, the database query joins on the wrong column, meaning the image lookup always fails to match the correct user. This should join on the user's ID, not the image row's own ID.

2. **User settings return an internal type instead of a model type.** The function that retrieves user web settings currently returns an internal protobuf type directly from the database layer. This should be changed to return the proper model type, with callers responsible for converting to protobuf when needed. This makes settings testable and decoupled from the wire format.

3. **Updating a user's password requires unrelated agent group data.** The user update function currently requires an agent user group to always be provided, even when the update only involves the password. The agent user group parameter should become optional (nullable), so that password-only updates can be performed without needing to supply unrelated information.

## Expected Behavior

- Profile image retrieval should return the correct image for the requesting user.
- The settings retrieval function should return the model type, and the model type should support conversion to the protobuf representation.
- User updates where no agent user group is provided should skip agent group operations and succeed.
- Updating just the password hash should work without providing an agent user group.

## Why This Matters

These bugs prevent profile images from being served and make the user settings layer harder to test and maintain. The password update restriction creates unnecessary coupling in the API.
