I'm working on our user management system and have run into several related issues I'd like to fix together.

First, retrieving a user's profile image doesn't work — the database query is joining profile image records on the wrong column, so images are never matched to the right user. It should be joining on the user's identifier, not the image row's own identifier.

Second, the function that fetches user web settings is returning an internal wire-format type directly from the database layer, instead of going through the proper model type. I'd like to change it so the database layer returns the model type, and the model type has a method to convert itself to the protobuf representation when needed. This makes the settings layer testable and properly layered.

Third, the user update function requires an agent user group to be provided every time, even when I'm only updating something like the user's password. The agent user group parameter should be made optional — when it's not provided, the agent group update steps should simply be skipped. This way, a password-only update can be done without passing unrelated data.

It would also be helpful to have a dedicated function for bulk-setting the active status of multiple users at once, since right now there's no clean way to activate or deactivate a list of users in one call.
