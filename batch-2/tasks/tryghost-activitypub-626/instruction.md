Implement relationship status indicators for accounts in follower and following lists within an ActivityPub-based social application. Ensure that each account entry includes fields indicating whether the viewing user follows or has blocked the account.

*   Update `AccountFollowsView.getFollowsByAccount`:
    *   Include a `followedByMe` boolean field for each account, set to true if the context account follows the listed account.
    *   Include a `blockedByMe` boolean field for each account, set to true if the context account has blocked the listed account.
    *   Ensure the `isFollowing` field matches the `followedByMe` value for each account.

*   Update `AccountFollowsView.getFollowsByRemoteLookUp`:
    *   Include `followedByMe` and `blockedByMe` boolean fields for each account.
    *   For accounts not in the local database, set both fields to false.
    *   Return an error with value 'invalid-next-parameter' if the `next` parameter's host differs from the actor's host.
    *   Return an error with value 'not-an-actor' if the URL resolves to a non-Actor ActivityPub object.

*   Ensure `AccountFollows` interface includes:
    *   An `accounts` array of `AccountInfo` objects.
    *   A `next` field that is either a string cursor or null.

*   Ensure each `AccountInfo` object contains:
    *   Fields: `id` (string), `name` (string), `handle` (string), `avatarUrl` (string or undefined), `isFollowing` (boolean), `followedByMe` (boolean), `blockedByMe` (boolean).

*   Update `FixtureManager` in `src/test/fixtures.ts`:
    *   Implement `createBlock(blocker: Account, blocked: Account)` to establish a block relationship in the database.

*   Ensure `createFixtureManager` function is exported from `src/test/fixtures.ts` and returns a `FixtureManager` instance with methods: `createInternalAccount`, `createFollow`, `createBlock`, and `reset`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.