Update the QUIC stream shutdown event structure to accurately reflect connection closure details. Implement necessary changes in the source files to distinguish between application and transport layer closures, and whether the closure was initiated by the remote peer.

*   Modify `QUIC_STREAM_EVENT::SHUTDOWN_COMPLETE` in `src/inc/msquic.h`:
    *   Rename `ConnectionShutdownByPeer` to `ConnectionShutdownByApp`.
    *   Add a new field `ConnectionClosedRemotely` as `BOOLEAN : 1`.
    *   Adjust `RESERVED` from 6 to 5 bits.

*   Update the `TestStream` class in `src/test/lib/TestStream.h` and `src/test/lib/TestStream.cpp`:
    *   Add the following member fields:
        *   `bool ConnectionShutdown : 1`
        *   `bool ConnectionShutdownByApp : 1`
        *   `bool ConnectionClosedRemotely : 1`
        *   `QUIC_UINT62 ConnectionErrorCode`
    *   Initialize these fields in the constructor to `false` or `0`.

*   Implement event handling in `TestStream::HandleStreamEvent` (in `src/test/lib/TestStream.cpp`):
    *   On receiving `QUIC_STREAM_EVENT_SHUTDOWN_COMPLETE`, capture:
        *   `Event->SHUTDOWN_COMPLETE.ConnectionShutdown`
        *   `Event->SHUTDOWN_COMPLETE.ConnectionShutdownByApp`
        *   `Event->SHUTDOWN_COMPLETE.ConnectionClosedRemotely`
        *   `Event->SHUTDOWN_COMPLETE.ConnectionErrorCode`
    *   Store these values in the corresponding member fields.

*   Add public getter methods in `TestStream`:
    *   `bool GetConnectionShutdown() const`
    *   `bool GetShutdownByApp() const`
    *   `bool GetClosedRemotely() const`
    *   `QUIC_UINT62 GetConnectionErrorCode() const`

*   Ensure correct behavior for connection shutdown scenarios:
    *   If the connection's `GetIsShutdown()` returns true, `GetConnectionShutdown()` must return true.
    *   `GetShutdownByApp()` and `GetClosedRemotely()` should match the connection's `GetPeerClosed()` value.
    *   If `GetTransportClosed()` is true, both `GetShutdownByApp()` and `GetClosedRemotely()` must return false.
    *   If the connection was peer-closed, `GetConnectionErrorCode()` must match `GetExpectedPeerCloseErrorCode()`.

*   For stream aborts without connection shutdown:
    *   `ConnectionShutdown`, `ConnectionShutdownByApp`, `ConnectionClosedRemotely` should be false.
    *   `ConnectionErrorCode` should be 0.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.