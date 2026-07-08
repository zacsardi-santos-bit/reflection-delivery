I'm cleaning up a bunch of related stuff in our user management system and want to knock these out together.

First bug: pulling a user's profile image just never works. The query that grabs the image is joining the profile image records on the wrong column, it's matching against the image row's own id instead of the user's id, so the lookup never lines up with the right user and images never come back. Needs to join on the user identifier so the correct image gets returned for whoever's asking.

Second, the function that fetches user web settings is handing back an internal protobuf wire-format type straight out of the database layer, which makes that layer annoying to test and couples everything to the wire format. I want the db layer to return the proper model type instead, and give the model type a method that converts itself to the protobuf representation so callers do that step when they actually need protobuf. Keeps the settings layer testable and properly decoupled.

Third, the user update function insists on an agent user group every single time, even when I'm literally just updating a password hash. Make that agent user group param optional/nullable, and when it's not passed just skip the agent group update steps entirely and let the update succeed. So a password-only update should go through fine without supplying that unrelated data.

Oh and while I'm in there, it'd be great to have a dedicated function for bulk-setting the active status across a list of users at once, since right now there's no clean way to activate or deactivate a bunch of users in a single call.
