Refactor the post publishing flow in your ActivityPub-based application to introduce a clean publishing pipeline with well-defined interfaces. Implement the necessary classes and methods to handle the full lifecycle of publishing a post, including resolving actors, building URIs, storing objects, managing the outbox, and sending activities.

*   Implement `FedifyActivitySender` in `src/activitypub/activity.ts`:
    *   Accept a `FedifyRequestContext` in the constructor.
    *   Implement `sendActivityToActorFollowers(activity: Activity, actor: Actor): Promise<void>`.
    *   Call the context's `sendActivity` method with `{ handle: actor.preferredUsername }`, the string `'followers'`, the activity, and the options object `{ preferSharedInbox: true }`.

*   Implement `FedifyActorResolver` in `src/activitypub/actor.ts`:
    *   Accept a `FedifyRequestContext` in the constructor.
    *   Implement `resolveActorByHandle(handle: string): Promise<Actor | null>`.
    *   Delegate to the context's `getActor` method with the handle and return the actor or null.

*   Implement `FedifyKvStoreObjectStore` in `src/activitypub/object.ts`:
    *   Accept a `KvStore` in the constructor.
    *   Implement `store(object: FedifyObject): Promise<void>`.
    *   Convert the object to JSON-LD via `toJsonLd()`, then call `kvStore.set` with key `[object.id.href]` and the JSON-LD value.
    *   Throw an error with the message 'Object can not be stored without an ID' if the object's id is null.

*   Implement `FedifyKvStoreOutbox` in `src/activitypub/outbox.ts`:
    *   Accept a `KvStore` in the constructor.
    *   Implement `add(activity: Activity): Promise<void>`.
    *   Call `addToList(kvStore, ['outbox'], activity.id.href)`.
    *   Throw an error with the message 'Activity can not be added to outbox without an ID' if the activity's id is null.

*   Implement `FedifyUriBuilder` in `src/activitypub/uri.ts`:
    *   Accept a `FedifyRequestContext` in the constructor.
    *   Implement `buildObjectUri(cls: ActivityPubObjectClass, id: string): URL`.
    *   Implement `buildFollowersCollectionUri(handle: string): URL`.

*   Define the `Post` interface in `src/publishing/service.ts` with the fields: `id`, `title`, `content`, `excerpt`, `featureImageUrl`, `publishedAt`, `url`, and `author`.

*   Implement `FedifyPublishingService` in `src/publishing/service.ts`:
    *   Accept `ActivitySender`, `ActorResolver`, `ObjectStore`, and `UriBuilder` in the constructor.
    *   Implement `publishPost(post: Post, outbox: Outbox): Promise<void>`.
    *   Resolve the actor using `post.author.handle`. Throw an error with the message 'Actor not resolved for handle: ' followed by the handle value if the actor cannot be resolved.
    *   Create and store exactly three ActivityPub objects in this order: a `Note`, an `Article`, and a `Create` instance, using the `ObjectStore`'s `store` method.
    *   Add the `Create` activity to the provided outbox by calling `outbox.add`.
    *   Send the `Create` activity to the resolved actor's followers by calling `activitySender.sendActivityToActorFollowers`.

*   Update `src/activitypub/index.ts` to re-export all interfaces and classes from the activitypub module, including `ActivitySender`, `FedifyActivitySender`, `ActorResolver`, `FedifyActorResolver`, `ObjectStore`, `FedifyKvStoreObjectStore`, `Outbox`, `FedifyKvStoreOutbox`, `UriBuilder`, and `FedifyUriBuilder`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.