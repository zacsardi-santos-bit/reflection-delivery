Improve the reliability of KubeVirt's device plugin manager by implementing automatic re-registration and retry logic for device plugins. Ensure that the system can detect socket file deletions and handle plugin restarts with appropriate backoff delays.

*   Implement automatic re-registration:
    *   Modify `healthCheck` in `pkg/virt-handler/device-manager/generic_device.go` to monitor the directory containing the device plugin socket file (`socketPath`).
    *   Log the message "device socket file for device <deviceName> was removed, kubelet probably restarted." when a filesystem remove event is detected on the socket file path.
    *   Ensure `healthCheck` returns `nil` when the socket file is removed or the stop channel is closed.
    *   Maintain device health as "Healthy" while the socket file and device file are present.

*   Implement retry logic for device plugins:
    *   Add a `backoff` field to `DeviceController` in `pkg/virt-handler/device-manager/device_controller.go` to configure delay durations between restart attempts when a device plugin exits with an error. Set default values to `[1s, 2s, 5s, 10s]`.
    *   In `startDevicePlugin`, immediately restart the plugin if `Start` returns `nil` (clean exit) without any delay.
    *   On error, increment the retry index (capped at the last element of `backoff`) and apply the corresponding delay before restarting.
    *   Ensure multiple device plugins are managed concurrently by `Run()`, allowing other plugins to start even if one is unavailable.

*   Update utility functions and constants:
    *   Export `SocketPath(deviceName string) string` in `pkg/virt-handler/device-manager/generic_device.go` to return the full path of the device plugin socket for a given device name.
    *   Export `KVMName` as a constant with the string value "kvm" in `pkg/virt-handler/device-manager/generic_device.go`.
    *   Ensure `ExecuteCommandOnPodV2` returns the error from remote command execution unconditionally, even with non-empty stdout/stderr output.

*   Ensure `GenericDevicePlugin` struct includes:
    *   A `done` field of type `chan struct{}` to signal internal goroutines to exit.
    *   A `deviceRoot` field of type `string` to resolve the absolute path to the device file.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.