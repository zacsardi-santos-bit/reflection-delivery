## Description

When users open an app's detail screen and view its components, there is no way to search through those components by keyword or sort them according to user preferences. The app may have dozens or hundreds of activities, services, receivers, and providers, but there is no structured mechanism to filter or reorder them.

Additionally, the internal naming of the component-detail repository contract is inconsistent with the naming conventions used for other repositories in the codebase — the current name implies it is an interface (via a naming prefix) rather than just a repository contract.

## Expected Behavior

- A search capability for app components should be available that:
  - Accepts a package name and an optional keyword
  - Returns components organized by category (activities, services, receivers, and providers)
  - Filters components to those whose name contains the keyword when one is provided
  - Returns all components for the package when no keyword is given
  - Sorts results according to the user's configured sort preference: alphabetically by short name or full qualified name, in ascending or descending order
  - Applies a secondary ordering based on the user's show-priority setting: disabled components first, enabled components first, or no priority
  - Identifies and flags running services within the results
- When no app is found for the given package name, an empty result should be returned
- The component-detail repository interface should be renamed to follow the same naming convention as other repository interfaces in the project (drop the "I" prefix)

## Why This Matters

Without the ability to search and sort components, navigating large component lists is cumbersome. Users need to quickly find a specific receiver or service by name and understand whether it is currently running or blocked. The naming cleanup also improves code consistency and makes dependency injection bindings easier to follow.
