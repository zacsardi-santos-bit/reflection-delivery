Update the jinja2 template expander service to listen on port 8443 by default instead of port 50051. Ensure that the service can still accept an explicit port argument to override the default. Verify that all template validation and evaluation functionalities work correctly on the new port.

*   Change the default port for the jinja2 expander gRPC server:
    *   Update the default port from 50051 to 8443.
    *   Ensure the server binds to port 8443 when started without explicit port arguments.
*   Implement command-line flag handling:
    *   Use a command-line flag named 'port' to specify the server port.
    *   Set the default value of the 'port' flag to 8443.
    *   Ensure the server listens on the specified port when the 'port' flag is used.
*   Verify server functionality:
    *   Ensure the server can successfully handle Validate and Evaluate operations on port 8443.
    *   Confirm that gRPC clients can connect to [::]:8443 and interact with the server as expected.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.