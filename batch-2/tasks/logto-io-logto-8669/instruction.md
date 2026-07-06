I'm working on extending our multi-tenant OIDC signing key management system to support scheduled key rotation.

*   The rotateOidcPrivateKeyStatuses function must be exported from packages/core/src/libraries/oidc-private-key.ts. When the input contains a key with Next status, it must return a new two-element array where the Next key is changed to Current status and the Current key is changed to Previous status; the old Previous key (if any) is dropped. When the input contains no Next key and all keys already have explicit status values, the function must return the original array reference unchanged (strict identity, not a new copy). When the input contains no Next key and some keys lack explicit statuses, the function must return the normalized array.

*   The createLogtoConfigQueries factory in packages/core/src/queries/logto-config.ts must include getSigningKeyRotationState in its return object. This function queries the logto_configs table selecting key and value columns where key equals LogtoTenantConfigKey.SigningKeyRotationState. It must return undefined when no row is found, and otherwise return the parsed value (a SigningKeyRotationState object with optional tenantCacheExpiresAt and signingKeyRotationAt number fields).

*   The createLogtoConfigQueries factory must include upsertSigningKeyRotationState in its return object. This function must perform a full upsert of the signing key rotation state: INSERT ... ON CONFLICT (tenantId, key) DO UPDATE SET value = $jsonbValue RETURNING value. It must return the parsed SigningKeyRotationState from the database row.

*   The createLogtoConfigQueries factory must include setTenantCacheExpiresAt(tenantCacheExpiresAt: number) in its return object. This function must perform a JSONB-merge upsert: INSERT with only the tenantCacheExpiresAt field, ON CONFLICT DO UPDATE SET value = COALESCE(value, '{}'::jsonb) || jsonb({tenantCacheExpiresAt}), RETURNING value. It must merge only tenantCacheExpiresAt while preserving any existing signingKeyRotationAt, and return the full merged SigningKeyRotationState.

*   The createLogtoConfigQueries factory must include setSigningKeyRotationAt(signingKeyRotationAt: number) in its return object. This function must perform a JSONB-merge upsert: INSERT with only the signingKeyRotationAt field, ON CONFLICT DO UPDATE SET value = COALESCE(value, '{}'::jsonb) || jsonb({signingKeyRotationAt}), RETURNING value. It must merge only signingKeyRotationAt while preserving any existing tenantCacheExpiresAt, and return the full merged SigningKeyRotationState.

*   The EnvSet.load() method must call promoteScheduledSigningKeyRotation() before calling getOidcConfigs(). Both functions must be invoked exactly once per load() call.

*   Tenant.invalidateCache() must call this.queries.logtoConfigs.setTenantCacheExpiresAt() with the current numeric timestamp (Date.now()), then sync the returned SigningKeyRotationState to wellKnownCache: store the full state object under 'signing-key-rotation-state' with WellKnownCache.defaultKey, and store the tenantCacheExpiresAt number under 'tenant-cache-expires-at' with WellKnownCache.defaultKey.

*   Tenant must expose a new public method scheduleSigningKeyRotation(timestamp: number): Promise<void>. It must call this.queries.logtoConfigs.setSigningKeyRotationAt(timestamp), then sync the returned SigningKeyRotationState to wellKnownCache: store the full state object under 'signing-key-rotation-state' with WellKnownCache.defaultKey, and store the tenantCacheExpiresAt value (if present) under 'tenant-cache-expires-at' with WellKnownCache.defaultKey.

*   Tenant.checkHealth() must use a cache-first strategy for the signing key rotation state. It must first check wellKnownCache for 'signing-key-rotation-state'. If a defined value is found in cache (including null), it must use it without querying the database. If the cache returns undefined (miss), it must call this.queries.logtoConfigs.getSigningKeyRotationState() and sync the result (even when undefined/null) to wellKnownCache to prevent future repeated DB queries. checkHealth() must return false when tenantCacheExpiresAt is set and the tenant was created at or before that timestamp, or when signingKeyRotationAt is set and is at or past the current time and the tenant was created before it. checkHealth() must return true otherwise.


*   Interface details: Type: Function
Name: rotateOidcPrivateKeyStatuses
Location: packages/core/src/libraries/oidc-private-key.ts
Signature: rotateOidcPrivateKeyStatuses(privateKeys: LogtoOidcConfigType['oidc.privateKeys']): OidcPrivateKey[]
Description: Promotes a staged Next signing key to Current and demotes the existing Current key to Previous. Returns a new two-element array when a Next key is found. When no Next key exists and all input keys already carry explicit status values, returns the original array reference unchanged. When no Next key exists and some keys lack explicit statuses, returns the normalized key array.

Type: Function (part of createLogtoConfigQueries return object)
Name: getSigningKeyRotationState
Location: packages/core/src/queries/logto-config.ts
Signature: getSigningKeyRotationState(): Promise<SigningKeyRotationState | undefined>
Description: Queries the logto_configs table for the row with key equal to LogtoTenantConfigKey.SigningKeyRotationState. Selects the key and value columns. Returns undefined when no matching row exists; otherwise returns the parsed SigningKeyRotationState value. The SigningKeyRotationState type has optional fields: tenantCacheExpiresAt (number) and signingKeyRotationAt (number).

Type: Function (part of createLogtoConfigQueries return object)
Name: upsertSigningKeyRotationState
Location: packages/core/src/queries/logto-config.ts
Signature: upsertSigningKeyRotationState(value: SigningKeyRotationState): Promise<SigningKeyRotationState>
Description: Inserts or fully replaces the signing key rotation state row. Uses INSERT ... ON CONFLICT (tenantId, key) DO UPDATE SET value = $value RETURNING value. Returns the parsed SigningKeyRotationState from the returned row.

Type: Function (part of createLogtoConfigQueries return object)
Name: setTenantCacheExpiresAt
Location: packages/core/src/queries/logto-config.ts
Signature: setTenantCacheExpiresAt(tenantCacheExpiresAt: number): Promise<SigningKeyRotationState>
Description: Inserts or partially updates the signing key rotation state row using JSONB merge. Uses INSERT ... VALUES (LogtoTenantConfigKey.SigningKeyRotationState, jsonb({tenantCacheExpiresAt})) ON CONFLICT DO UPDATE SET value = COALESCE(value, '{}'::jsonb) || jsonb({tenantCacheExpiresAt}) RETURNING value. Merges only the tenantCacheExpiresAt field, preserving any existing signingKeyRotationAt. Returns the full merged SigningKeyRotationState.

Type: Function (part of createLogtoConfigQueries return object)
Name: setSigningKeyRotationAt
Location: packages/core/src/queries/logto-config.ts
Signature: setSigningKeyRotationAt(signingKeyRotationAt: number): Promise<SigningKeyRotationState>
Description: Inserts or partially updates the signing key rotation state row using JSONB merge. Uses INSERT ... VALUES (LogtoTenantConfigKey.SigningKeyRotationState, jsonb({signingKeyRotationAt})) ON CONFLICT DO UPDATE SET value = COALESCE(value, '{}'::jsonb) || jsonb({signingKeyRotationAt}) RETURNING value. Merges only the signingKeyRotationAt field, preserving any existing tenantCacheExpiresAt. Returns the full merged SigningKeyRotationState.

Type: Method
Name: scheduleSigningKeyRotation
Location: packages/core/src/tenants/Tenant.ts
Signature: scheduleSigningKeyRotation(timestamp: number): Promise<void>
Description: New public method on the Tenant class. Records a scheduled future key activation by calling this.queries.logtoConfigs.setSigningKeyRotationAt(timestamp). Syncs the returned SigningKeyRotationState to wellKnownCache under the 'signing-key-rotation-state' key (using WellKnownCache.defaultKey). Also syncs tenantCacheExpiresAt (if present in the returned state) to wellKnownCache under 'tenant-cache-expires-at'.

Type: Method (updated behavior)
Name: invalidateCache
Location: packages/core/src/tenants/Tenant.ts
Signature: invalidateCache(): Promise<void>
Description: Updated behavior — must call this.queries.logtoConfigs.setTenantCacheExpiresAt(Date.now()) (passing a numeric timestamp), then sync the returned SigningKeyRotationState to wellKnownCache. The full state must be stored under 'signing-key-rotation-state' with WellKnownCache.defaultKey. The tenantCacheExpiresAt numeric value must also be stored under 'tenant-cache-expires-at' with WellKnownCache.defaultKey.

Type: Method (updated behavior)
Name: checkHealth
Location: packages/core/src/tenants/Tenant.ts
Signature: checkHealth(): Promise<boolean>
Description: Updated behavior — must use a cache-first strategy. First calls wellKnownCache.get('signing-key-rotation-state', WellKnownCache.defaultKey). If the cached value is defined (including null), uses it directly without querying the database. If the cached value is undefined (cache miss), calls this.queries.logtoConfigs.getSigningKeyRotationState() and syncs the result (even if undefined/null) to wellKnownCache to prevent repeated DB misses. Returns false if tenantCacheExpiresAt is set and the tenant was created at or before that timestamp. Returns false if signingKeyRotationAt is set and its value is less than or equal to Date.now() and is greater than the tenant creation time. Returns true otherwise.

Type: Function (part of createLogtoConfigLibrary return object)
Name: promoteScheduledSigningKeyRotation
Location: packages/core/src/libraries/logto-config.ts
Signature: promoteScheduledSigningKeyRotation(): Promise<void>
Description: Checks whether a staged signing key has a scheduled activation timestamp that has already passed, and if so, promotes the staged key to active status by updating the stored signing keys. This function is called once per load() invocation, before getOidcConfigs() is called.

Type: Method (updated behavior)
Name: load
Location: packages/core/src/env-set/index.ts
Signature: load(customDomain?: string): Promise<void>
Description: Updated behavior — must call promoteScheduledSigningKeyRotation() before calling getOidcConfigs(). Both functions must be called exactly once per load() invocation.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.