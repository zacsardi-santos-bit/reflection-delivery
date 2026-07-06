I'm working on the API operations layer for a command-line tool that lists resources from a server.

*   The execute_list method must include the limit parameter as a query parameter in every HTTP GET request it sends to the server — i.e., the params dict passed to client.get must contain a 'limit' key whose value equals the limit argument supplied to execute_list.

*   The limit parameter must be forwarded on all paginated requests, not only the first one. Every GET call made during multi-page iteration must include params['limit'] equal to the original limit argument.

*   Paginated results must still be aggregated correctly across pages: all items from all pages must be combined and returned in the final response object.


*   Interface details: Type: Class
Name: BaseOperations
Location: airflow-ctl/src/airflowctl/api/operations.py
Description: Base class for API operations that handles list pagination and HTTP requests to the Airflow server.

Type: Method
Name: execute_list
Location: airflow-ctl/src/airflowctl/api/operations.py
Signature: execute_list(self, path: str, data_model: type[T], limit: int = 50, params: dict | None = None) -> T | ServerResponseError
Description: Fetches a paginated list of resources from the server. Must include the `limit` value in the `params` dict passed to every `client.get` call (both the initial request and all subsequent paginated requests).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.