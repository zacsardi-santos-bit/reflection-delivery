I'm working on two related improvements to the Lexical editor codebase.

*   The custom mock event and clipboard classes (for keyboard events, data transfer, and clipboard data) must be removed from the shared test utility file at packages/lexical/src/__tests__/utils/index.tsx; these classes must no longer be exported from that module.

*   The tabKeyboardEvent() utility function in packages/lexical/src/__tests__/utils/index.tsx must return a native KeyboardEvent constructed with type 'keydown' and key 'Tab'.

*   The shiftTabKeyboardEvent() utility function in packages/lexical/src/__tests__/utils/index.tsx must return a native KeyboardEvent constructed with type 'keydown', key 'Tab', and shiftKey set to true.

*   The jsdom test environment must provide working native DataTransfer, ClipboardEvent, and DragEvent constructors on globalThis. These polyfills must be registered in vitest.setup.mts (only when running under jsdom) so that all test files can use new DataTransfer(), new ClipboardEvent(...), and new DragEvent(...) without importing any mock class.

*   The DragEvent polyfill must support passing a dataTransfer option in the constructor (so that new DragEvent('drop', {dataTransfer}) correctly sets the dataTransfer property), and must not make its methods non-configurable so that tests can override them with Object.defineProperty.

*   The ClipboardEvent polyfill must support passing a clipboardData option in the constructor (so that new ClipboardEvent('paste', {clipboardData}) correctly sets the clipboardData property).

*   When a valid URL is pasted (via the paste command) while plain text is selected inside a paragraph, the link extension must wrap the selected text in a LinkNode. The LinkNode's getURL() must return the pasted URL, and the LinkNode's getTextContent() must return the originally selected text.

*   When a valid URL is pasted while text is selected inside a code block, the link extension must NOT create a LinkNode. The code block's children must contain no LinkNode after the paste.


*   Interface details: Type: Function
Name: tabKeyboardEvent
Location: packages/lexical/src/__tests__/utils/index.tsx
Signature: tabKeyboardEvent() -> KeyboardEvent
Description: Returns a native KeyboardEvent constructed with type 'keydown' and {key: 'Tab'}. The previous implementation using the custom KeyboardEventMock class must be replaced.

Type: Function
Name: shiftTabKeyboardEvent
Location: packages/lexical/src/__tests__/utils/index.tsx
Signature: shiftTabKeyboardEvent() -> KeyboardEvent
Description: Returns a native KeyboardEvent constructed with type 'keydown' and {key: 'Tab', shiftKey: true}. The previous implementation using the custom KeyboardEventMock class must be replaced.

Note: The following classes must be REMOVED (no longer exported) from packages/lexical/src/__tests__/utils/index.tsx:
- DataTransferMock
- EventMock
- KeyboardEventMock
- ClipboardDataMock

Note: The global vitest test setup file (vitest.setup.mts) must register polyfills for the following constructors on globalThis when running under jsdom. Each polyfill must only be installed if the constructor is not already present (guard with typeof check):
- DataTransfer: must support setData(type, value), getData(type), clearData(type?), and a types getter
- ClipboardEvent: must extend Event, accept {clipboardData} in the constructor options, and expose clipboardData on instances
- DragEvent: must extend MouseEvent, accept {dataTransfer} in the constructor options, expose dataTransfer on instances, and must NOT define preventDefault as non-configurable (tests override it with Object.defineProperty)

Note: The paste-to-link logic inside packages/lexical-link/src/LexicalLinkExtension.ts must be updated so that the URL-paste-to-link conversion is skipped when any selected node is a text node that is not "simple text" (e.g., code highlight nodes inside a code block). The existing check that skips element nodes must be extended to also skip when the selection contains non-simple text nodes.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.