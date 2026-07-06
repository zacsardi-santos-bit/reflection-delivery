I'm working on the Backstage catalog backend and I'd like to add metrics observability to the part of the system that handles source control events.

*   The DefaultCatalogScmEventsService constructor must be updated to accept a MetricsAPI parameter (from @opentelemetry/api); this metrics instance is used internally to create an OpenTelemetry counter for tracking event actions.

*   DefaultCatalogScmEventsService must implement a markEventActionTaken method that accepts an object with an 'action' property (string) and an optional 'count' property (number, defaults to 1). Calling markEventActionTaken({ action: 'refresh' }) must result in calling counter.add(1, { action: 'refresh' }) on the internal OTel counter.

*   The CatalogScmEventsService interface (the type used by DefaultLocationStore and GenericScmEventRefreshProvider) must be updated to include the markEventActionTaken method signature: markEventActionTaken(options: { count?: number; action: string }): void.

*   When DefaultLocationStore handles a location.deleted SCM event that results in catalog locations being removed, it must call scmEvents.markEventActionTaken({ count: 1, action: 'delete' }).

*   When DefaultLocationStore handles a location.deleted SCM event that also results in a new catalog location being created (e.g. the URL changed), it must additionally call scmEvents.markEventActionTaken({ count: 1, action: 'create' }).

*   When DefaultLocationStore handles a repository.deleted SCM event, it must call scmEvents.markEventActionTaken({ count: 1, action: 'delete' }).

*   When DefaultLocationStore handles a repository.moved SCM event, it must call scmEvents.markEventActionTaken({ count: 1, action: 'delete' }).

*   When DefaultLocationStore handles a location.moved SCM event, it must call scmEvents.markEventActionTaken({ count: 1, action: 'move' }).


*   Interface details: Type: Class
Name: DefaultCatalogScmEventsService
Location: plugins/catalog-node/src/scmEvents/DefaultCatalogScmEventsService.ts
Description: Service for managing catalog SCM events, now with integrated metrics tracking. The constructor must be updated to accept a MetricsAPI parameter (from @opentelemetry/api), which is used to create an OpenTelemetry counter. The new markEventActionTaken method records that an action was taken in response to an SCM event by incrementing this counter.
Signature: constructor(metrics: MetricsAPI)
Signature: markEventActionTaken(options: { count?: number; action: string }): void

Type: Interface
Name: CatalogScmEventsService
Location: plugins/catalog-node/src/scmEvents/types.ts
Description: The interface/type for the catalog SCM events service must include the markEventActionTaken method so that consumers such as DefaultLocationStore and GenericScmEventRefreshProvider can call it on the service instance.
Signature: markEventActionTaken(options: { count?: number; action: string }): void


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.