Implement a mechanism to allow conditional edge functions to return multiple tool calls as packets, enabling parallel invocation of the same node with different arguments in a single graph step. Ensure proper error handling for state schema mismatches and manage message updates correctly.

*   Define a NamedTuple `Packet` in `langgraph/constants.py`:
    *   Fields: `node` (str), `arg` (Any).
    *   Importable via `from langgraph.constants import Packet`.

*   Update conditional edge functions to:
    *   Return a list of `Packet` instances.
    *   Invoke the specified node once per `Packet` with `arg` as input.

*   Modify graph output streaming:
    *   Nodes invoked via `Packet` should have outputs grouped as a list under the node name (e.g., `{'tools': [output1, output2]}`).
    *   Nodes invoked normally should output as a single dict (e.g., `{'agent': {'messages': ...}}`).

*   Ensure state management reflects multiple packets:
    *   `StateSnapshot.next` should list the node name once per pending packet (e.g., `next=('tools', 'tools')`).

*   Implement error handling for state schema mismatches:
    *   Raise `InvalidUpdateError` if a node returns a key not in the declared state schema.

*   Manage extra keys in node return values:
    *   Allow extra keys to be accessed by downstream conditional edge functions.
    *   Prevent extra keys from being persisted in the checkpoint state.

*   Update message handling in state updates:
    *   Replace existing messages in-place when `update_state` or `aupdate_state` is called with a message ID that matches an existing one.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.