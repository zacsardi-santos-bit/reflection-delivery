## Description

Two related improvements are needed for the Lexical test infrastructure and link extension.

**1. Replace custom event mocks with native browser APIs**

The shared test utility file currently exports several hand-rolled mock classes that stand in for native browser event and clipboard APIs (keyboard events, drag-and-drop data transfer, clipboard events, etc.). These mocks were necessary because the test environment didn't support these APIs natively. The mocks have grown stale and diverge from real browser behavior in subtle ways that can hide bugs. The fix is to remove these mock classes entirely and instead provide lightweight polyfills directly in the global test setup, so all tests interact with the same API surface as production code.

**2. Context-aware URL paste in the link extension**

When a user selects text in a regular paragraph and pastes a URL, the link extension should automatically wrap the selected text in a hyperlink pointing to that URL. This is a common editor UX pattern (supported by tools like Google Docs and Notion).

However, the conversion must be context-aware: if the user pastes a URL while editing inside a code block, no link wrapping should happen. Code blocks are not prose, and turning code content into hyperlinks would be incorrect and unexpected.

## Expected Behavior

- Removing the mock event/clipboard classes from the test utilities; tests use native browser APIs directly
- The global test setup registers polyfills for the clipboard data, clipboard event, and drag-and-drop event browser APIs so native constructors work in the test environment
- Pasting a URL over selected paragraph text wraps that text in a link with the correct URL
- Pasting a URL over selected text inside a code block leaves the code block unchanged with no link node created

## Why This Matters

Using native APIs in tests makes failures more realistic and reduces maintenance burden. The URL-paste-to-link feature improves editing productivity without breaking the code-editing experience.
