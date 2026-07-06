Create a dedicated transport handler module for the Ledger Live Desktop application to manage USB/HID device communication. Implement functions that handle device connection, command exchange, event listening, and connection closure, ensuring each operation returns an observable stream of structured responses.

*   Implement `transportOpen` in `apps/ledger-live-desktop/src/internal/transportHandler.ts`:
    *   Accept a `params` object with a device descriptor and request ID.
    *   Return an Observable emitting a response object with a `data` field if a transport is available.
    *   Emit an error object with `name: "CantOpenDevice"` if no transport module supports the descriptor.

*   Implement `transportExchange` in `apps/ledger-live-desktop/src/internal/transportHandler.ts`:
    *   Accept a `params` object with a device descriptor, APDU hex string, and request ID.
    *   Return an Observable emitting a response object with a `data` field containing the hex string of the response buffer.
    *   Emit an error object with `name: "DisconnectedDeviceDuringOperation"` if no transport is open or the exchange fails.

*   Implement `transportExchangeBulk` in `apps/ledger-live-desktop/src/internal/transportHandler.ts`:
    *   Accept a `params` object with a device descriptor, an array of APDU hex strings, and request ID.
    *   Return an Observable emitting one response per APDU: success as `{ data: <hex string> }`, failure as `{ error: { name: "DisconnectedDeviceDuringOperation", message: string } }`.
    *   Emit a single error object if no transport is open.

*   Implement `transportListen` in `apps/ledger-live-desktop/src/internal/transportHandler.ts`:
    *   Accept a `params` object with a request ID.
    *   Return an Observable emitting objects of shape `{ data: { type: "add" | "remove", descriptor: string } }` for each device event.

*   Implement `transportListenUnsubscribe` in `apps/ledger-live-desktop/src/internal/transportHandler.ts`:
    *   Accept a `params` object with a request ID.
    *   Return an Observable that completes immediately without emitting items.
    *   Ensure the corresponding `transportListen` observable completes and stops emitting events.

*   Implement `transportClose` in `apps/ledger-live-desktop/src/internal/transportHandler.ts`:
    *   Accept a `params` object with a device descriptor and request ID.
    *   Return an Observable emitting `{ type: "ok", data: <original params.data> }` on success.
    *   Emit an error object with `name: "DisconnectedDeviceDuringOperation"` if no transport is open.
    *   Call the underlying transport's `close()` method and remove the transport from the internal store.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.