Implement caching mechanisms to track the MQTT protocol version and authentication credential name for each client session in the MQTT broker. Ensure these details are stored when a client connects and are removed appropriately when sessions are cleared.

*   Update the `CacheConstants` class:
    *   Define `CLIENT_MQTT_VERSION_CACHE` as a String constant for storing each client's MQTT protocol version.
    *   Define `CLIENT_SESSION_CREDENTIALS_CACHE` as a String constant for storing each client's authentication credential name.

*   Implement the `CacheNameResolver` class:
    *   Ensure it is a Spring-injectable bean.
    *   Provide a method `getCache(String cacheName)` that returns a Spring `Cache` instance by name.

*   Modify `ConnectServiceImpl`:
    *   Declare `CacheNameResolver` as a constructor-injected dependency.
    *   On successful connection, store the client's MQTT version name in `CLIENT_MQTT_VERSION_CACHE` using the client ID as the key.

*   Update `SessionClusterManagerImpl`:
    *   Declare `CacheNameResolver` as a constructor-injected dependency.
    *   Evict entries from both `CLIENT_MQTT_VERSION_CACHE` and `CLIENT_SESSION_CREDENTIALS_CACHE` when a non-persistent session is cleared.
    *   Ensure entries are evicted when `processClearSession` fully removes a session.

*   Enhance `BasicMqttClientAuthProvider`:
    *   Store the authenticated credential name into `CLIENT_SESSION_CREDENTIALS_CACHE` using the client ID as the key upon successful authentication.

*   Ensure the following behaviors:
    *   `CLIENT_MQTT_VERSION_CACHE.get(clientId, String.class)` returns 'MQTT_3_1_1' or 'MQTT_5' after a client connects with the respective protocol version.
    *   After a non-persistent client disconnects and its session is removed, `CLIENT_MQTT_VERSION_CACHE.get(clientId, String.class)` returns null.
    *   For persistent sessions, cache entries remain until the session is fully cleared.
    *   `CLIENT_SESSION_CREDENTIALS_CACHE.get(clientId, String.class)` returns the credentials name after authentication and becomes null after session clearance.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.