Refactor the resource management system to use agent and agent execution model objects instead of raw numeric IDs for building file paths and creating resource records. Implement methods to format paths using both the name and ID of agents or executions, and update the system to use these methods for reading and writing operations.

*   Implement database lookup methods:
    *   In `superagi/models/agent.py`, create a class method `get_agent_from_id(session: Session, agent_id: int) -> Agent` to query the database and return the Agent object matching the given ID.
    *   In `superagi/models/agent_execution.py`, create a class method `get_agent_execution_from_id(session: Session, agent_execution_id: int) -> AgentExecution` to query the database and return the AgentExecution object matching the given ID.

*   Update path formatting in `superagi/helper/resource_helper.py`:
    *   Implement `get_formatted_agent_level_path(agent: Agent, path: str) -> str` to replace `{agent_id}` in the path with `{agent.name}_{agent.id}`.
    *   Implement `get_formatted_agent_execution_level_path(agent_execution: AgentExecution, path: str) -> str` to replace `{agent_execution_id}` in the path with `{agent_execution.name}_{agent_execution.id}`.

*   Refactor resource path methods:
    *   Implement `get_agent_write_resource_path(file_name: str, agent: Agent, agent_execution: AgentExecution) -> str` to resolve write paths using Agent and AgentExecution objects.
    *   Implement `get_agent_read_resource_path(file_name: str, agent: Agent, agent_execution: AgentExecution) -> str` to resolve read paths using Agent and AgentExecution objects.

*   Update resource creation method:
    *   Implement `make_written_file_resource(file_name: str, agent: Agent, agent_execution: AgentExecution) -> Resource` to create a Resource object using Agent and AgentExecution objects. Ensure the Resource object includes:
        *   Name equal to `file_name`.
        *   Path as `'resources/{file_name}'`.
        *   Storage type from configuration.
        *   Size from the file size on disk.
        *   Type as `'application/{extension}'`.
        *   Channel as `'OUTPUT'`.
        *   `agent_id` equal to `agent.id`.

*   Ensure tools interacting with agent resources:
    *   Use updated resource path methods with Agent and AgentExecution objects.
    *   Use a session from `toolkit_config` to look up objects by their IDs.
    *   Store and use `agent_execution_id` in addition to `agent_id`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.