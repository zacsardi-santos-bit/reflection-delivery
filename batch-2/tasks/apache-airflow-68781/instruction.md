I'm working on the CloudWatch log integration in Airflow.

*   The get_cloudwatch_logs method must catch cloud provider errors that indicate the requested log stream does not exist (error code 'ResourceNotFoundException'), and instead of raising, must yield exactly one event dict as a hint to the caller.

*   The single yielded event dict for a missing log stream must have a 'message' key whose value contains the substring 'No log stream found in CloudWatch' and also contains the log stream name that was requested.

*   When get_cloudwatch_logs encounters a cloud provider error with any error code other than 'ResourceNotFoundException', it must re-raise the exception so that genuine errors (such as access denials) propagate to the caller.


*   Interface details: Type: Method
Name: get_cloudwatch_logs
Location: providers/amazon/src/airflow/providers/amazon/aws/log/cloudwatch_task_handler.py
Signature: get_cloudwatch_logs(self, stream_name: str, task_instance) -> Generator
Description: Retrieves CloudWatch log events for a given log stream and task instance. Returns a generator of event dicts (each with at least a "message" key). When the log stream does not exist (cloud provider reports a resource-not-found error), the method must not raise; instead it must yield exactly one event dict whose "message" contains the text "No log stream found in CloudWatch" and the stream name. Any other cloud provider error must be re-raised.

Type: Class
Name: CloudWatchRemoteLogIO
Location: providers/amazon/src/airflow/providers/amazon/aws/log/cloudwatch_task_handler.py
Description: Remote log I/O class for CloudWatch. The get_cloudwatch_logs method lives on this class. It is accessed via the `io` attribute of the CloudwatchTaskHandler.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.