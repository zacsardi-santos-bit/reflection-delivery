Implement a search-and-sort feature for app components in the Blocker app. Update the component-detail repository interface to align with project naming conventions. Ensure the search functionality allows filtering by keyword and sorting based on user preferences, while also identifying running services.

*   Update the ComponentDetailRepository interface:
    *   Rename from IComponentDetailRepository to ComponentDetailRepository.
    *   Ensure FakeComponentDetailRepository and TestComponentDetailRepository implement ComponentDetailRepository.
    *   Bind ComponentDetailRepository in the DI module.

*   Implement the SearchComponentsUseCase class:
    *   Constructor signature: `constructor(userDataRepository: UserDataRepository, appRepository: AppRepository, componentRepository: ComponentRepository, componentDetailRepository: ComponentDetailRepository, getServiceController: GetServiceControllerUseCase, cpuDispatcher: CoroutineDispatcher)`.
    *   Method signature: `operator fun invoke(packageName: String, keyword: String = ""): Flow<ComponentSearchResult>`.
    *   Return a Flow emitting a ComponentSearchResult.
    *   Emit ComponentSearchResult(app = null) with empty lists if no app is found for the package name.
    *   Return all components for the package when the keyword is empty, categorized by type.
    *   Filter components by keyword (case-insensitive) when provided, categorized by type.
    *   Sort components based on user preferences:
        *   By COMPONENT_NAME or PACKAGE_NAME.
        *   In ASCENDING or DESCENDING order.
    *   Apply secondary sorting based on ComponentShowPriority:
        *   DISABLED_COMPONENTS_FIRST or ENABLED_COMPONENTS_FIRST.
    *   Identify running services and set isRunning = true for those in the service list.

*   Define the ComponentSearchResult data class:
    *   Location: `core/domain/src/main/kotlin/com/merxury/blocker/core/domain/model/ComponentSearchResult.kt`.
    *   Fields: app (nullable AppItem), activity, service, receiver, provider (each a List<ComponentInfo> defaulting to empty).

*   Update TestComponentDetailRepository:
    *   Implement ComponentDetailRepository.
    *   Method signature: `fun sendComponentDetail(componentDetail: List<ComponentDetail>)`.

*   Update FakeServiceController:
    *   Method signature: `fun sendRunningServices(vararg name: String)`.
    *   Ensure isServiceRunning returns true for injected service names.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.