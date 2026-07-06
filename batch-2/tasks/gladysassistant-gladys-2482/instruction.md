I'm working on the Zigbee2mqtt integration in Gladys and I need to add a full "reset" capability.

*   The removeContainer function must return undefined (not the Docker API return value) on a successful container removal.

*   When the Docker engine returns an HTTP 404 status code during container removal (container does not exist), removeContainer must resolve successfully and return undefined instead of throwing.

*   When the Docker engine returns any other error during container removal (e.g., HTTP 500), removeContainer must rethrow the original error with its statusCode and message intact.

*   The stopContainer function must return undefined (not the Docker API return value) on a successful container stop.

*   When the Docker engine returns an HTTP 304 status code during container stop (container is already stopped), stopContainer must resolve successfully and return undefined instead of throwing.

*   When the Docker engine returns any other error during container stop (e.g., HTTP 500), stopContainer must rethrow the original error with its statusCode and message intact.

*   A new HTTP POST endpoint at /api/v1/service/zigbee2mqtt/reset must exist. When called, it must invoke the zigbee2mqttManager reset method and respond with JSON: { success: true }. The route must require authentication and admin privileges.

*   The reset method must be located at server/services/zigbee2mqtt/lib/reset.js, exported as { reset }, and attached to Zigbee2mqttManager.prototype.reset.

*   When reset is called, it must invoke disconnect (which internally calls stopContainer and removeContainer for both the MQTT and zigbee2mqtt containers — resulting in exactly 2 stop calls and 2 remove calls).

*   The reset method must destroy exactly 12 configuration variables for the service by calling gladys.variable.destroy(key, serviceId) for each of: 'ZIGBEE2MQTT_DRIVER_PATH', 'Z2M_BACKUP', 'ZIGBEE_DONGLE_NAME', 'Z2M_MQTT_MODE', 'Z2M_TCP_PORT', 'Z2M_MQTT_URL', 'Z2M_MQTT_USERNAME', 'Z2M_MQTT_PASSWORD', 'GLADYS_MQTT_USERNAME', 'GLADYS_MQTT_PASSWORD', 'DOCKER_MQTT_VERSION', 'DOCKER_Z2M_VERSION'.

*   The reset method must call gladys.system.getGladysBasePath(), then delete the 'zigbee2mqtt' subfolder under the returned basePathOnContainer value using fs.rm with options { recursive: true, force: true }. If deletion fails, the error must propagate.

*   After reset, the following in-memory state properties must be set: discoveredDevices={}, topicBinds={}, usbConfigured=false, mqttExist=false, mqttRunning=false, mqttContainerRunning=false, zigbee2mqttExist=false, zigbee2mqttRunning=false, gladysConnected=false, zigbee2mqttConnected=false, z2mPermitJoin=false, coordinatorFirmware=null, z2mContainerError=null. The properties dockerBased and networkModeValid must not be modified.

*   After resetting state, reset must call emitStatusEvent() to broadcast the updated status.

*   If an mqttClient is set when reset is called, the method must call mqttClient.end(), mqttClient.removeAllListeners(), and then set mqttClient to null.

*   If a backupScheduledJob is set when reset is called, the method must call backupScheduledJob.cancel().


*   Interface details: Type: Function
Name: removeContainer
Location: server/lib/system/system.removeContainer.js
Signature: removeContainer(containerId, options = {}) -> Promise<undefined>
Description: Removes a Docker container by ID. Returns undefined on success. Silently resolves (returns undefined) when Docker returns HTTP 404 (container does not exist). Rethrows the error for all other Docker error status codes.

Type: Function
Name: stopContainer
Location: server/lib/system/system.stopContainer.js
Signature: stopContainer(containerId) -> Promise<undefined>
Description: Stops a Docker container by ID. Returns undefined on success. Silently resolves (returns undefined) when Docker returns HTTP 304 (container already stopped). Rethrows the error for all other Docker error status codes.

Type: Function
Name: reset
Location: server/services/zigbee2mqtt/lib/reset.js
Signature: reset() -> Promise<void>
Description: Resets the Zigbee2mqtt integration to a clean factory state. Must be exported as { reset } from the module and assigned to Zigbee2mqttManager.prototype.reset. Performs the following steps in order:
1. Calls this.disconnect() to stop and remove both the MQTT and zigbee2mqtt Docker containers.
2. Destroys all 12 configuration variables by calling gladys.variable.destroy(key, serviceId) for each key: 'ZIGBEE2MQTT_DRIVER_PATH', 'Z2M_BACKUP', 'ZIGBEE_DONGLE_NAME', 'Z2M_MQTT_MODE', 'Z2M_TCP_PORT', 'Z2M_MQTT_URL', 'Z2M_MQTT_USERNAME', 'Z2M_MQTT_PASSWORD', 'GLADYS_MQTT_USERNAME', 'GLADYS_MQTT_PASSWORD', 'DOCKER_MQTT_VERSION', 'DOCKER_Z2M_VERSION'.
3. Calls gladys.system.getGladysBasePath() to get the basePathOnContainer, then deletes <basePathOnContainer>/zigbee2mqtt using fs.rm(path, { recursive: true, force: true }). Propagates any error from this operation.
4. Resets in-memory instance properties: discoveredDevices={}, topicBinds={}, usbConfigured=false, mqttExist=false, mqttRunning=false, mqttContainerRunning=false, zigbee2mqttExist=false, zigbee2mqttRunning=false, gladysConnected=false, zigbee2mqttConnected=false, z2mPermitJoin=false, coordinatorFirmware=null, z2mContainerError=null. Does NOT modify dockerBased or networkModeValid.
5. Calls this.emitStatusEvent().
6. If this.mqttClient is set: calls mqttClient.end(), mqttClient.removeAllListeners(), sets this.mqttClient = null.
7. If this.backupScheduledJob is set: calls backupScheduledJob.cancel().

Type: Route
Name: post /api/v1/service/zigbee2mqtt/reset
Location: server/services/zigbee2mqtt/api/zigbee2mqtt.controller.js
Signature: POST /api/v1/service/zigbee2mqtt/reset
Description: HTTP endpoint that triggers a full reset of the Zigbee2mqtt integration. Must be authenticated and admin-only. Calls zigbee2mqttManager.reset() and responds with JSON: { success: true }.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.