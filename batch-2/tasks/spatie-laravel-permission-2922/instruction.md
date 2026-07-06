I'm working with a Laravel permissions package and I keep running into an ergonomics problem: I can only assign or remove roles from the user's side, but there's no way to do it from the role's side.

*   The Role model must expose a syncModels method that replaces all existing model-role associations for that role with the given set of models; models that were previously assigned but are absent from the new set must lose the role, and models in the new set must gain it.

*   When syncModels is called with an empty collection or array, all existing model-role associations for the role must be removed.

*   syncModels must deduplicate its inputs — passing the same model instance or the same ID more than once must result in exactly one pivot record, not multiple.

*   The Role model must expose an assignToModels method that grants the role to the given models without touching any existing assignments; models that already have the role must not gain a duplicate pivot record.

*   assignToModels must be idempotent: calling it repeatedly for the same model produces exactly one pivot record in the model_has_roles table.

*   assignToModels must deduplicate inputs: passing the same model or ID twice in a single call creates only one pivot record.

*   The Role model must expose a removeFromModels method that revokes the role from the given models while leaving all other role assignments untouched; calling it for a model that does not have the role must produce no error and no change.

*   All three methods (syncModels, assignToModels, removeFromModels) must accept any of the following as their first argument: a single Eloquent model instance, a single integer or string ID, an array of model instances, an array of IDs, or a mixed array of model instances and IDs.

*   All three methods accept an optional second argument that is the fully-qualified model class name; when raw IDs are passed as the first argument and a class name is supplied as the second argument, that class is used to resolve the IDs to model records.

*   When raw IDs are passed without an explicit model class, the methods must fall back to the value of the permission.models.default_model configuration key (if set) to determine which model class to use for resolving those IDs.

*   The HasAssignedModels trait must be used by the Role model so that all three methods are available on Role instances.


*   Interface details: Type: Trait
Name: HasAssignedModels
Location: src/Traits/HasAssignedModels.php
Description: Trait that provides bulk model-assignment operations on the Role model. Must be used by the Role model (src/Models/Role.php).
Signature:
  assignToModels(array|Collection|Model|int|string $models, ?string $modelClass = null): static
  removeFromModels(array|Collection|Model|int|string $models, ?string $modelClass = null): static
  syncModels(array|Collection|Model|int|string $models, ?string $modelClass = null): static

Type: Method
Name: assignToModels
Location: src/Traits/HasAssignedModels.php (used via Role model)
Signature: assignToModels(array|Collection|Model|int|string $models, ?string $modelClass = null): static
Description: Assigns the role to the given models without removing existing role assignments. Accepts a single model instance, a single ID, an array of model instances, or an array of IDs. Deduplicates so no duplicate pivot records are created. Idempotent — already-assigned models are not re-inserted.

Type: Method
Name: removeFromModels
Location: src/Traits/HasAssignedModels.php (used via Role model)
Signature: removeFromModels(array|Collection|Model|int|string $models, ?string $modelClass = null): static
Description: Removes the role from the given models, leaving all other model assignments intact. Accepts a single model instance, a single ID, an array of model instances, or an array of IDs. Safe to call if a model does not already have the role.

Type: Method
Name: syncModels
Location: src/Traits/HasAssignedModels.php (used via Role model)
Signature: syncModels(array|Collection|Model|int|string $models, ?string $modelClass = null): static
Description: Replaces ALL existing model-role associations for this role with the given set. Accepts a single model instance, a single ID, an array of model instances, or an array of IDs. An empty array removes all associations. Deduplicates inputs so no duplicate pivot records are created.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.