## Description

The resource management system currently uses raw numeric IDs (agent ID, agent execution ID) when building file paths and creating resource records. This approach makes directory structures hard to read and trace back to a specific agent or execution run. We need to refactor the system to use actual agent and agent execution model objects instead of bare IDs, enabling human-readable, descriptive paths.

## Expected Behavior

- The resource helper should accept agent and agent execution objects (not just numeric IDs) when constructing file paths or resource records.
- File paths should be structured using both the name and the numeric ID of the agent or execution (e.g., a combination of the agent's name and its ID, separated by an underscore), making it easy to identify which agent produced which files.
- Separate methods should exist for resolving read paths and write paths for agent resources.
- The agent model should support looking up an agent record from the database by its ID.
- The agent execution model should support looking up an execution record from the database by its ID.
- Tools that handle agent files (such as file readers or email attachments) should use the updated resource path methods and look up agent/execution objects from the database using a session, rather than passing raw IDs.

## Why This Matters

This change makes the on-disk layout of agent resources human-readable and traceable. Instead of anonymous numeric folders, each agent's output directory will reflect both the agent's name and its ID, improving debuggability and making it easier to inspect what files were produced by which agent or execution run.
