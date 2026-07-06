Implement the XDS override host load balancing policy to handle non-READY connectivity states of targeted override host subchannels. Ensure that picks are queued or rerouted based on the subchannel's state, and initiate re-resolution requests as necessary.

*   Handle pick requests based on subchannel states:
    *   If the subchannel is in the READY state, route the pick to that subchannel.
    *   If the subchannel is in the CONNECTING state, queue the pick until the connection is established.
    *   If the subchannel is in the IDLE state, queue the pick and actively request a connection.
    *   If the subchannel is in the TRANSIENT_FAILURE state, do not honor the override; instead, fall through to the child load balancing policy and use remaining ready subchannels.
*   Manage subchannel state transitions:
    *   Post a re-resolution request to the channel helper when a tracked override-host subchannel transitions to the IDLE state.
    *   Post a re-resolution request to the channel helper when a tracked override-host subchannel transitions to the TRANSIENT_FAILURE state.
*   Maintain policy connectivity state:
    *   Track the current connectivity state of each subchannel to make routing decisions without holding locks during the pick operation.
    *   Report the overall policy connectivity state as READY to the parent as long as some subchannels are ready, regardless of the state of individual override-targeted subchannels.
*   Implement the feature in the file `src/core/ext/filters/client_channel/lb_policy/xds/xds_override_host.cc`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.