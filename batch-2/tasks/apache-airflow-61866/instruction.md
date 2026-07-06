I'm working on improving the Redis provider hook in Apache Airflow to support client identification when connecting to Redis.

*   The module airflow.providers.redis.hooks.redis must expose a module-level attribute named DriverInfo (the DriverInfo class imported from the Redis client library, or None if the library version does not provide it).

*   The module airflow.providers.redis.hooks.redis must expose a module-level boolean attribute named _SUPPORTS_LIB_NAME that reflects whether the installed Redis client library supports the lib_name constructor keyword argument.

*   When DriverInfo is available, RedisHook.get_conn() must instantiate DriverInfo with no arguments, call add_upstream_driver with the string 'apache-airflow-providers-redis' as the first positional argument, and pass the resulting object as the driver_info keyword argument to the Redis client constructor.

*   When DriverInfo is available, RedisHook.get_conn() must NOT pass lib_name as a keyword argument to the Redis client constructor.

*   When DriverInfo is None (unavailable) and _SUPPORTS_LIB_NAME is True, RedisHook.get_conn() must pass a lib_name keyword argument to the Redis client constructor whose value contains the string 'apache-airflow-providers-redis', and must NOT pass driver_info.

*   When DriverInfo is None and _SUPPORTS_LIB_NAME is False, RedisHook.get_conn() must pass neither driver_info nor lib_name to the Redis client constructor.

*   RedisHook.get_conn() must continue to pass all existing connection parameters (host, username, password, port, db, ssl, ssl_cert_reqs, ssl_ca_certs, ssl_keyfile, ssl_certfile, ssl_check_hostname) as keyword arguments to the Redis client constructor, regardless of which client identification mode is active.


*   Interface details: Type: Module Attribute
Name: DriverInfo
Location: providers/redis/src/airflow/providers/redis/hooks/redis.py
Description: A module-level attribute that holds the DriverInfo class imported from the Redis client library, or None if the installed version of the library does not provide it. Tests patch this attribute directly on the module.

Type: Module Attribute
Name: _SUPPORTS_LIB_NAME
Location: providers/redis/src/airflow/providers/redis/hooks/redis.py
Description: A module-level boolean flag indicating whether the installed Redis client library supports the lib_name keyword argument in its client constructor. Tests patch this attribute directly on the module.

Type: Method
Name: get_conn
Location: providers/redis/src/airflow/providers/redis/hooks/redis.py
Signature: get_conn(self) -> Redis
Description: Opens and returns a Redis client connection. Must implement three-way branching based on DriverInfo and _SUPPORTS_LIB_NAME:
  1. If DriverInfo is not None: instantiate DriverInfo(), call .add_upstream_driver("apache-airflow-providers-redis") on it, and pass the result as driver_info= to the Redis() constructor. Do not pass lib_name.
  2. If DriverInfo is None and _SUPPORTS_LIB_NAME is True: pass lib_name= (containing "apache-airflow-providers-redis") to the Redis() constructor. Do not pass driver_info.
  3. If DriverInfo is None and _SUPPORTS_LIB_NAME is False: pass neither driver_info nor lib_name to the Redis() constructor.
  In all cases, pass all existing connection parameters (host, username, password, port, db, ssl, ssl_cert_reqs, ssl_ca_certs, ssl_keyfile, ssl_certfile, ssl_check_hostname) as keyword arguments to Redis().


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.