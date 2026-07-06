I'm working with a nested editor setup where I have a parent editor and a child editor.

*   When a command is dispatched on a child editor that has NestedEditorExtension registered, and the parent editor is NOT currently running an update, the command must synchronously propagate to the parent editor's registered command handlers after the child's own handlers have run.

*   When command propagation is synchronous (parent not updating), dispatchCommand returns the result of the command dispatch to the parent (true if any parent handler returns true).

*   When a command dispatched by a child editor propagates to the parent, the parent's command handler must receive the child editor as its editor argument (not the parent editor), but $getEditor() within the parent handler must return the parent editor.

*   When a command is dispatched on a child editor while the parent editor IS currently running an update, the child's command handlers run synchronously within the current parent update. The return value of dispatchCommand is false (only reflecting the child-side result), and the parent's handlers are NOT called yet at that point.

*   After the parent editor's active update completes, any deferred command propagation from a child editor must trigger the parent's command handlers exactly once.


*   Interface details: Type: Class
Name: NestedEditorExtension
Location: packages/lexical-extension/src/index.ts
Description: An extension that, when registered on a child editor, causes commands dispatched by the child to propagate to the parent editor after the child's own command handlers have run. If the parent editor is currently in the middle of an update when the child dispatches the command, the propagation to the parent is deferred until the parent's update finishes.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.