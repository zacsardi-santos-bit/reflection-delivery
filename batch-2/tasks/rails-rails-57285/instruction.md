I'm working on improving how Rails manages the lifecycle of view reloaders.

*   The ViewReloader class must provide a class-level factory method called 'create' that accepts a watcher keyword argument, instantiates a new ViewReloader, registers the instance's hook with ActionView::PathRegistry.file_system_resolver_hooks, and returns the created reloader.

*   The ViewReloader class must provide an instance method called 'hook' that returns a bound method object representing the reloader's registration callback, which can be stored in and retrieved from ActionView::PathRegistry.file_system_resolver_hooks by object identity.

*   The ViewReloader class must provide an instance method called 'deactivate' that removes the reloader's hook from ActionView::PathRegistry.file_system_resolver_hooks.

*   The ViewReloader#deactivate method must be idempotent: calling it multiple times must not raise an error and must leave the hooks collection at the same size as after the first call.

*   A new class Rails::Application::ReloadersCollection must be implemented at railties/lib/rails/application/reloaders_collection.rb. It must support adding reloaders with the << operator.

*   Rails::Application::ReloadersCollection#clear must call deactivate on each reloader that responds to deactivate, then remove all reloaders from the collection so that it becomes empty.

*   Rails::Application::ReloadersCollection#clear must not raise an error when the collection contains reloaders that do not implement a deactivate method.

*   Rails::Application::ReloadersCollection#delete must remove the specified reloader from the collection, call deactivate on it, and leave all other reloaders in the collection unaffected and not deactivated.

*   Rails::Application::ReloadersCollection must include Enumerable behavior, supporting methods such as to_a, any?, size, and empty?.


*   Interface details: Type: Class
Name: ActionView::CacheExpiry::ViewReloader
Location: actionview/lib/action_view/cache_expiry.rb
Description: Existing class extended with new class method and new instance methods for lifecycle management.
Signature:
  self.create(watcher:) -> ViewReloader   # Creates a new instance and registers its hook on ActionView::PathRegistry.file_system_resolver_hooks
  hook() -> Method                        # Returns the bound method object used for PathRegistry registration; same object identity across calls
  deactivate() -> void                    # Removes the reloader's hook from ActionView::PathRegistry.file_system_resolver_hooks; idempotent

Type: Class
Name: Rails::Application::ReloadersCollection
Location: railties/lib/rails/application/reloaders_collection.rb
Description: A new enumerable collection class for managing the lifecycle of Rails reloaders. Supports adding, removing, and deactivating reloaders.
Signature:
  <<(reloader) -> self                    # Adds a reloader to the collection
  clear() -> void                         # Calls deactivate on each reloader responding to it, then empties the collection
  delete(reloader) -> reloader            # Removes the given reloader and calls deactivate on it; other reloaders are untouched
  each(&block) -> Enumerator              # Iterates over all reloaders; enables Enumerable methods (to_a, any?, size, empty?, etc.)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.