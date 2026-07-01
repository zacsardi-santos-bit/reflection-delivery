Implement a clean stop mechanism for the rosbag2 player to terminate playback without shutting down ROS. Ensure the player can be stopped programmatically, remotely via a ROS service call, and automatically when the player object is destroyed.

*   Implement a public `stop()` method in the Player class:
    *   Signature: `void stop()`
    *   Location: `rosbag2_transport/include/rosbag2_transport/player.hpp`
    *   Ensure it signals the playback loop to exit.
    *   If called before `play()`, it must return immediately without blocking.
    *   If called while paused, it must unblock the pause-wait loop and return within approximately 1 second.
    *   If called during active playback, it must allow the current message to finish before exiting the loop.
    *   Ensure the destructor implicitly calls `stop()` to terminate any ongoing playback within a short timeout.

*   Update the Player constructor:
    *   Accept an optional fourth parameter `node_name` of type `std::string`.
    *   Default value: `"rosbag2_player"`.
    *   Signature: `Player(std::unique_ptr<rosbag2_cpp::Reader> reader, const rosbag2_storage::StorageOptions & storage_options, const rosbag2_transport::PlayOptions & play_options, const std::string & node_name = "rosbag2_player")`.

*   Define a new ROS service interface named `Stop`:
    *   Location: `rosbag2_interfaces/srv/Stop.srv`
    *   Signature: `(empty request) --- (empty response)`
    *   Register in `rosbag2_interfaces` CMakeLists.txt for compilation and accessibility as `rosbag2_interfaces::srv::Stop`.

*   Register a ROS service endpoint:
    *   Name: `~/stop`
    *   Location: `rosbag2_transport/src/rosbag2_transport/player.cpp` (inside `create_control_services`)
    *   Type: `rosbag2_interfaces::srv::Stop`
    *   Ensure it invokes the `stop()` method when called.
    *   Make it discoverable and connectable by service clients during normal operation.

*   Update the MockPlayer class:
    *   Location: `rosbag2_transport/test/rosbag2_transport/mock_player.hpp`
    *   Constructor must accept an optional `node_name` parameter of type `std::string`.
    *   Default value: `"rosbag2_mock_player"`.
    *   Forward this parameter to the Player base class constructor.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.