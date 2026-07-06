## Description

VS Code supports contributed chat session providers via extensions. These providers can supply custom conversation sessions, but there's currently no proper infrastructure for them to support conversation forking — the ability to branch a conversation thread at a specific message and continue it as a separate session.

There is a deprecated mechanism for fork support on the session object itself, but it is not consistently propagated to the host side, and the host-side session management does not correctly advertise fork support to the UI or properly route fork requests.

## Expected Behavior

- The service interface responsible for managing chat sessions must expose methods for checking whether a loaded session supports forking, and for triggering a fork at a given conversation point.
- The mock implementation of this service used in tests must stub these new methods.
- The protocol between the extension host and the main thread must include a new message type that triggers a fork on the extension side.
- The data transfer object for session content must include a flag indicating whether fork support is available for a given session.
- On the host side, when a session's content is initialized and fork support is declared, the session object must expose a fork function that marshals the request across the extension boundary and properly revives any resource references in the result.
- On the extension host side, if a session item controller has registered a fork handler, that handler must take priority over any deprecated fork handler set on the session object. The fork handler must receive the session resource and a request turn object that corresponds to the conversation point being forked from.

## Why This Matters

Without this infrastructure, contributed chat session providers have no reliable way to support conversation forking, even if the underlying provider is capable of it. This change enables the fork UI control to be shown and wired up correctly for contributed sessions, and ensures the fork operation works end-to-end across the extension boundary.
