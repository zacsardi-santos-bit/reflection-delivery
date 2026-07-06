I'm building AI agents that handle complex, multi-step tasks, and I need a way for agents to maintain a persistent todo list within a session.

*   TodoProvider must be a public sealed class in the Microsoft.Agents.AI namespace that extends AIContextProvider, with a parameterless constructor, located at dotnet/src/Microsoft.Agents.AI/Harness/Todo/TodoProvider.cs.

*   When invoked via InvokingAsync, TodoProvider must return an AIContext with non-null Instructions and exactly 5 Tools.

*   TodoItem must be a public sealed class with properties: Id (int), Title (string), Description (string?, nullable), and IsComplete (bool, false by default).

*   TodoItemInput must be a class with properties: Title (string) and Description (string?, nullable). It is used as the input model for creating new todo items.

*   TodoState must hold the persisted state with a List<TodoItem> Items (defaults to empty) and int NextId (defaults to 1). It is stored in the session's StateBag under the string key "TodoProvider" and must be retrievable via session.StateBag.TryGetValue<TodoState>("TodoProvider", ...) using AgentJsonUtilities.DefaultOptions.

*   The AddTodos tool (exact name: "AddTodos", parameter name: "todos" of type List<TodoItemInput>) must create one or more TodoItem entries, assign each an auto-incrementing integer Id (starting at 1 for the first item per session), set IsComplete to false, and persist the updated state. Multiple items added in a single call must receive sequential IDs.

*   The CompleteTodos tool (exact name: "CompleteTodos", parameter name: "ids" of type List<int>) must set IsComplete=true on all matching items and return an integer count of items that were actually found and marked complete. It must return 0 when none of the given IDs exist.

*   The RemoveTodos tool (exact name: "RemoveTodos", parameter name: "ids" of type List<int>) must remove all matching items and return an integer count of items removed. It must return 0 when none of the given IDs exist.

*   The GetRemainingTodos tool (exact name: "GetRemainingTodos", no parameters) must return a JSON array containing only todo items where IsComplete is false. Each element must expose a "title" property.

*   The GetAllTodos tool (exact name: "GetAllTodos", no parameters) must return a JSON array containing all todo items regardless of completion status.

*   Todo state must persist across multiple InvokingAsync calls on the same session: items added during one invocation must be visible in tools obtained during a subsequent invocation on the same session.

*   TodoProvider.GetAllTodos(AgentSession? session) must return an IReadOnlyList<TodoItem> containing all todo items. For a brand-new session with no prior state, it must return an empty list.

*   TodoProvider.GetRemainingTodos(AgentSession? session) must return a List<TodoItem> containing only items where IsComplete is false.

*   AgentJsonUtilities must register JsonSerializable entries for TodoState, TodoItem, TodoItemInput, List<int> (TypeInfoPropertyName "IntList"), List<TodoItem> (TypeInfoPropertyName "TodoItemList"), and List<TodoItemInput> (TypeInfoPropertyName "TodoItemInputList") so that the session state bag and tool arguments serialize and deserialize correctly.


*   Interface details: Type: Class
Name: TodoProvider
Location: dotnet/src/Microsoft.Agents.AI/Harness/Todo/TodoProvider.cs
Namespace: Microsoft.Agents.AI
Description: An AIContextProvider that gives agents todo list management capabilities. Extends AIContextProvider. Stores state in session under the key "TodoProvider".
Signature:
  TodoProvider() — parameterless constructor
  Task<AIContext> InvokingAsync(AIContextProvider.InvokingContext context) — returns AIContext with non-null Instructions and exactly 5 Tools
  IReadOnlyList<TodoItem> GetAllTodos(AgentSession? session) — returns all todo items; returns empty list for a new session
  List<TodoItem> GetRemainingTodos(AgentSession? session) — returns only todo items where IsComplete is false

Type: Class
Name: TodoItem
Location: dotnet/src/Microsoft.Agents.AI/Harness/Todo/TodoItem.cs
Namespace: Microsoft.Agents.AI
Description: Represents a single todo item. Public sealed class.
Properties:
  int Id — auto-incremented integer ID starting at 1 for the first item in a session
  string Title — the title of the item
  string? Description — optional description, may be null
  bool IsComplete — completion flag, defaults to false on creation

Type: Class
Name: TodoItemInput
Location: dotnet/src/Microsoft.Agents.AI/Harness/Todo/TodoItemInput.cs
Namespace: Microsoft.Agents.AI
Description: Input model for creating a new todo item via the AddTodos tool.
Properties:
  string Title — required title
  string? Description — optional description, may be null

Type: Class
Name: TodoState
Location: dotnet/src/Microsoft.Agents.AI/Harness/Todo/TodoState.cs
Namespace: Microsoft.Agents.AI
Description: Holds the persisted todo list state in the session's StateBag under the key "TodoProvider". Accessible via session.StateBag.TryGetValue<TodoState>("TodoProvider", out var state, AgentJsonUtilities.DefaultOptions).
Properties:
  List<TodoItem> Items — the list of all todo items, defaults to an empty list
  int NextId — the next ID to assign; starts at 1 and increments with each new item

Note: AgentJsonUtilities (dotnet/src/Microsoft.Agents.AI/AgentJsonUtilities.cs) must include [JsonSerializable] registrations for TodoState, TodoItem, TodoItemInput, List<int> (TypeInfoPropertyName "IntList"), List<TodoItem> (TypeInfoPropertyName "TodoItemList"), and List<TodoItemInput> (TypeInfoPropertyName "TodoItemInputList").

Note on AI tool names: The five tools exposed by TodoProvider must be registered with exactly these names:
  "AddTodos"          — parameter name: "todos" (List<TodoItemInput>)
  "CompleteTodos"     — parameter name: "ids" (List<int>), returns int
  "RemoveTodos"       — parameter name: "ids" (List<int>), returns int
  "GetRemainingTodos" — no parameters, returns JSON array
  "GetAllTodos"       — no parameters, returns JSON array


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.