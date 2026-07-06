Implement a structured auditing service for the Backstage backend framework to record audit events with lifecycle stages. Create two classes: a root-level auditor and a plugin-scoped child auditor. Integrate this service into the catalog backend plugin's router.

*   Implement `DefaultRootAuditorService` in `packages/backend-defaults/src/entrypoints/auditor/Auditor.ts`:
    *   Include a static `create(options?: RootAuditorOptions): DefaultRootAuditorService` method to instantiate the service.
    *   Implement `forPlugin(deps: { auth: AuthService; httpAuth: HttpAuthService; plugin: PluginMetadataService }): AuditorService` to return a `DefaultAuditorService` instance.

*   Implement `DefaultAuditorService` in `packages/backend-defaults/src/entrypoints/auditor/Auditor.ts`:
    *   Provide an `async createEvent(options: { eventId: string; meta?: JsonObject; severityLevel?: AuditorServiceEventSeverityLevel; request?: Request }): Promise<AuditorServiceEvent>` method:
        *   Log the event with status 'initiated' using a private `log` method.
        *   Return an event object with methods for recording success or failure.
    *   Ensure the event object includes:
        *   `async success({ meta? }): Promise<void>` method:
            *   Log the event with status 'succeeded'.
            *   Merge root meta from `createEvent` with any additional meta.
            *   Ensure meta is always present, defaulting to `{}`.
        *   `async fail({ error, meta? }): Promise<void>` method:
            *   Log the event with status 'failed'.
            *   Serialize the error using `error.toString()`.
            *   Merge root meta from `createEvent` with any additional meta.
            *   Ensure meta is always present, defaulting to `{}`.
    *   Use a private `log` method with signature `async log(options: { eventId: string; status: 'initiated' | 'succeeded' | 'failed'; meta?: JsonObject; error?: string }): Promise<void>`:
        *   For 'initiated' status, include meta if provided.
        *   For 'succeeded' and 'failed' statuses, always include meta (default to `{}`).
        *   Ensure error is included for 'failed' status.

*   Update `createRouter` in `plugins/catalog-backend/src/service/createRouter.ts`:
    *   Accept an `auditor` option of type `AuditorService` in the `RouterOptions`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.