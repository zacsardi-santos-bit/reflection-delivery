Update the Java startup script to automatically configure proxy settings from environment variables for JVM invocations. Ensure the script reads standard proxy environment variables and translates them into appropriate Java system properties.

*   Implement logic in `fish-pepper/run-java-sh/fp-files/run-java.sh` to handle the following environment variables:
    *   `HTTP_PROXY`: 
        *   Extract the hostname and port from the URL.
        *   Add `-Dhttp.proxyHost=<hostname>` and `-Dhttp.proxyPort=<port>` to JVM arguments.
    *   `HTTPS_PROXY`: 
        *   Extract the hostname and port from the URL.
        *   Add `-Dhttps.proxyHost=<hostname>` and `-Dhttps.proxyPort=<port>` to JVM arguments.
    *   `no_proxy` or `NO_PROXY`: 
        *   Convert the comma-separated list of hostnames to a pipe-separated format.
        *   Add `-Dhttp.nonProxyHosts="<host1>|<host2>"` to JVM arguments.
*   Ensure no proxy-related JVM properties are added if none of the proxy environment variables are set.
*   Ensure that each proxy setting is added independently based on the presence of its corresponding environment variable.
*   Ensure the script exits with status 0 in all scenarios, whether proxy settings are configured or not.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.