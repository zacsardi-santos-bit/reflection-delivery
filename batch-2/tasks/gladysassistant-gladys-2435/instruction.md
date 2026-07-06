I'm working on the Zigbee2MQTT integration in my smart home system and need help implementing several related features.

*   The getContainerLogs function must be implemented in server/lib/system/system.getContainerLogs.js and attached to System.prototype. It must throw PlatformNotCompatible when this.dockerode is undefined. When dockerode is present, it must call dockerode.getContainer(containerId) and return the result of container.logs() with default options { stdout: true, stderr: true, tail: 100, follow: false } merged with any caller-provided options (caller options take precedence).

*   The configureContainer function must return an object { configChanged: boolean, adapterChanged: boolean } instead of a plain boolean. configChanged must be true when any configuration content changed (MQTT credentials, adapter type, port, etc.) or when the config file was created. adapterChanged must be true only when the serial.adapter value in the YAML file changed. A change limited to MQTT credentials must produce { configChanged: true, adapterChanged: false }. No change at all must produce { configChanged: false, adapterChanged: false }.

*   A new adapter protocol key 'ember' must be added to CONFIG_KEYS (value: 'ember') in server/services/zigbee2mqtt/adapters/index.js. ADAPTERS_BY_CONFIG_KEY must include entries for CONFIG_KEYS.EMBER listing dongles by their plain name, and entries for CONFIG_KEYS.EZSP listing the same dongles with the suffix ' (legacy ezsp)' appended to each name.

*   The ADAPTERS constant must be an array of objects { label: string, configKey: string } sorted alphabetically by label (locale-aware ascending sort). Each adapter/mode combination must appear as a separate entry. getManagedAdapters() must return this array of objects.

*   The Zigbee2mqttManager must have two new properties initialized to null: coordinatorFirmware and z2mContainerError. Both must be included in the payload of every STATUS_CHANGE websocket event and in the return value of status().

*   When a 'zigbee2mqtt/bridge/info' MQTT message is received in handleMqttMessage, if coordinator.meta is present, this.coordinatorFirmware must be set to { ...coordinator.meta, type: coordinator.type }. If coordinator.meta is absent, this.coordinatorFirmware must be set to null. emitStatusEvent() must be called in both cases.

*   When installZ2mContainer calls configureContainer and adapterChanged is true and the container already exists, it must stop the existing container, remove it, create a new one, and fetch a fresh container reference before restarting. When adapterChanged is false, it must only restart the existing container. After restarting the container, readZ2mContainerLogs(container.id) must be called as a fire-and-forget (non-blocking) operation.

*   The readZ2mContainerLogs function must be implemented as a method on Zigbee2mqttManager (in server/services/zigbee2mqtt/lib/readZ2mContainerLogs.js). It must call this.gladys.system.getContainerLogs(containerId, { follow: true }) to get a streaming log. For each line, it must strip the first character (Docker stream prefix byte), strip ANSI color codes matching the pattern /\x1B\[[0-9;]*[A-Za-z]/g, and strip remaining control characters before analysis.

*   readZ2mContainerLogs must detect the EZSP protocol version error when a log line contains both 'Adapter EZSP protocol version' and 'is not supported by Host'. When detected, this.z2mContainerError must be set to { code: 'EZSP_PROTOCOL_VERSION', message: null } and the stream must be destroyed. This known error code takes precedence over any generic error lines.

*   readZ2mContainerLogs must detect generic errors by matching lines against /error:/i (case-insensitive). When multiple such lines are found, only the last one is kept. If no known error code matches, this.z2mContainerError is set to { code: null, message: <last matched trimmed line> }. If no error lines are found, this.z2mContainerError is set to null.

*   readZ2mContainerLogs must resolve after 30 seconds if the stream never emits 'end' or 'error'. On stream 'error' events or when getContainerLogs throws, this.z2mContainerError must be set to null and the function must still resolve normally. In all cases, this.emitStatusEvent() must be called after completion.


*   Interface details: Type: Function
Name: getContainerLogs
Location: server/lib/system/system.getContainerLogs.js
Signature: getContainerLogs(containerId: string, options?: object) -> Promise<EventEmitter>
Description: Gets the log stream for a Docker container. Throws PlatformNotCompatible when not running in a Docker context (i.e., this.dockerode is undefined). Merges provided options with defaults { stdout: true, stderr: true, tail: 100, follow: false }. The function is exported as { getContainerLogs } and attached to the System prototype as System.prototype.getContainerLogs.

Type: Function
Name: readZ2mContainerLogs
Location: server/services/zigbee2mqtt/lib/readZ2mContainerLogs.js
Signature: readZ2mContainerLogs(containerId: string) -> Promise<void>
Description: Reads Zigbee2MQTT container logs and detects fatal errors. Calls this.gladys.system.getContainerLogs(containerId, { follow: true }). For each log line, strips the Docker stream prefix byte (first character), ANSI color codes, and remaining control characters before analysis. Detects EZSP protocol version mismatch when a line contains both the substring "Adapter EZSP protocol version" and "is not supported by Host" — sets this.z2mContainerError = { code: 'EZSP_PROTOCOL_VERSION', message: null } and destroys the stream. Detects generic errors when a line matches /error:/i — tracks the last such line as { code: null, message: <trimmed line> }. Known error codes take precedence over generic error lines. On stream error event or if getContainerLogs throws, sets this.z2mContainerError = null. Resolves after a 30-second timeout if the stream never ends. Always calls this.emitStatusEvent() after completion. Exported as { readZ2mContainerLogs } and attached to Zigbee2mqttManager.prototype.

Type: Method (change to existing)
Name: configureContainer
Location: server/services/zigbee2mqtt/lib/configureContainer.js
Signature: configureContainer(basePathOnContainer: string, config: object, setupMode?: boolean) -> Promise<{ configChanged: boolean, adapterChanged: boolean }>
Description: Previously returned a boolean. Now returns an object with two boolean fields: configChanged (true when any part of the configuration changed, including adapter or MQTT settings) and adapterChanged (true only when the serial.adapter value in the YAML changed). adapterChanged is independent of MQTT credential changes — MQTT-only changes produce { configChanged: true, adapterChanged: false }.

Type: Constant/Object (change to existing)
Name: CONFIG_KEYS
Location: server/services/zigbee2mqtt/adapters/index.js
Description: Must include EMBER: 'ember' in addition to the existing DECONZ, EZSP, and ZSTACK keys. ADAPTERS_BY_CONFIG_KEY[CONFIG_KEYS.EMBER] should contain the list of ember-capable dongle names (without legacy suffix). ADAPTERS_BY_CONFIG_KEY[CONFIG_KEYS.EZSP] should contain the legacy versions of the same dongles with the suffix " (legacy ezsp)".

Type: Constant (change to existing)
Name: ADAPTERS
Location: server/services/zigbee2mqtt/adapters/index.js
Description: Now an array of objects { label: string, configKey: string } sorted by label (ascending, locale-aware). Each adapter entry has a human-readable label and the corresponding configKey ('zstack', 'deconz', 'ember', or 'ezsp'). Adapters available in multiple modes appear as separate entries.

Type: Method (change to existing)
Name: getManagedAdapters
Location: server/services/zigbee2mqtt/lib/getManagedAdapters.js
Signature: getManagedAdapters() -> Array<{ label: string, configKey: string }>
Description: Returns the full ADAPTERS list. Now returns objects with label and configKey fields instead of plain strings.

Type: State properties (new fields on Zigbee2mqttManager)
Name: coordinatorFirmware, z2mContainerError
Location: server/services/zigbee2mqtt/lib/index.js
Description: Both initialized to null in the constructor. coordinatorFirmware holds { majorrel, minorrel, maintrel, revision, type } when coordinator firmware info is available from bridge messages, otherwise null. z2mContainerError holds { code: string|null, message: string|null } when a container error is detected, otherwise null. Both must be included in the status payload returned by status() and in every STATUS_CHANGE websocket event.

Type: Method (change to existing — bridge/info handling)
Name: handleMqttMessage
Location: server/services/zigbee2mqtt/lib/handleMqttMessage.js
Description: Must handle the MQTT topic 'zigbee2mqtt/bridge/info'. When coordinator.meta exists in the parsed message, sets this.coordinatorFirmware = { ...coordinator.meta, type: coordinator.type }. When coordinator.meta is absent, sets this.coordinatorFirmware = null. Calls this.emitStatusEvent() in both cases.

Type: Method (change to existing — adapter recreation)
Name: installZ2mContainer
Location: server/services/zigbee2mqtt/lib/installZ2mContainer.js
Description: Destructures the return of configureContainer as { configChanged, adapterChanged }. When adapterChanged is true and the container already exists (no creation needed), the existing container is stopped, removed, a new container is created, and the container reference is refreshed before restart. After restarting the container, calls this.readZ2mContainerLogs(container.id) as a fire-and-forget (non-blocking) call.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.