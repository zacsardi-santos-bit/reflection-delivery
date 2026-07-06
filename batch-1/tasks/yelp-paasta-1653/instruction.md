Convert all drain method operations to asynchronous coroutines to integrate with the async event loop architecture. Use an async-compatible HTTP client for all HTTP communications within these methods. Update the deployment orchestration code to properly await these async operations.

*   Implement all lifecycle methods in the `DrainMethod` base class and its subclasses as async coroutines:
    *   `drain(task)`, `stop_draining(task)`, `is_draining(task)`, and `is_safe_to_kill(task)` must use `async def` and be awaitable.
*   Update `HacheckDrainMethod` class methods:
    *   Implement `get_spool(task)` as an async coroutine using `aiohttp.ClientSession` as an async context manager.
        *   Access the response's HTTP status via `response.status`.
        *   Retrieve the response body text by awaiting `response.text()`.
    *   Implement `is_draining(task)` to return `True` if the HTTP status is 503 and `False` if 200.
    *   Ensure `drain(task)`, `stop_draining(task)`, and `is_safe_to_kill(task)` are async coroutines.
*   Update `HTTPDrainMethod` class methods:
    *   Implement `issue_request(url_spec, task)` as an async coroutine using `aiohttp.ClientSession`.
        *   Use `session.request` with keyword arguments: `method=...`, `url=...`, `headers=...`, `timeout=15`.
        *   Access the response's HTTP status via `response.status`.
    *   Ensure `drain(task)`, `stop_draining(task)`, `is_draining(task)`, and `is_safe_to_kill(task)` are async coroutines.
*   Modify functions in `setup_marathon_job.py` to await drain method coroutines:
    *   `deploy_service`, `get_tasks_by_state`, `drain_tasks_and_find_tasks_to_kill`, and `undrain_tasks` must use an asyncio event loop to await `drain`, `stop_draining`, `is_draining`, and `is_safe_to_kill`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.