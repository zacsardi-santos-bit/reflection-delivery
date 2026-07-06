I'm working on the CloudTrail log unmarshaler in the AWS logs encoding extension.

*   The CloudTrailLogUnmarshaler must expose a NewLogsDecoder method that accepts an io.Reader and zero or more decoder options, and returns an encoding.LogsDecoder and an error.

*   The returned encoding.LogsDecoder must implement a DecodeLogs() method that returns the next batch of plog.Logs and an error; it must return io.EOF (as the error) when the stream is exhausted, and continue returning io.EOF on all subsequent calls after the stream is exhausted.

*   The returned encoding.LogsDecoder must implement an Offset() method that returns the current stream position as int64.

*   The encoding package must expose a WithFlushItems option function that configures how many log records are included in each batch returned by DecodeLogs(); passing 1 causes each call to DecodeLogs() to return exactly one log record.

*   The encoding package must expose a WithOffset option function (accepting int64) that configures the starting position in the stream; for S3 Records format the offset is a record count and the decoder skips that many records before beginning to decode.

*   For S3 Records format (CloudTrail JSON with a top-level 'Records' array): when NewLogsDecoder is called with WithFlushItems(1) and WithOffset(n), the decoder skips the first n records, then on each DecodeLogs() call returns one record as plog.Logs matching the expected content for that record index, and Offset() returns the count of records processed so far (starting from n).

*   For CloudWatch subscription filter format and CloudTrail digest format: when NewLogsDecoder is called with a non-zero offset value, the first DecodeLogs() call must return io.EOF immediately, and Offset() must return the total byte length of the consumed input (e.g. 1878 bytes for the CloudWatch test file, 1214 bytes for the digest test file).

*   Test data files must exist at testdata/stream/cloudtrail_log.json (a CloudTrail S3 Records file with 3 records), and testdata/stream/cloudtrail_log_expect_1.yaml, cloudtrail_log_expect_2.yaml, cloudtrail_log_expect_3.yaml (golden files for the expected plog.Logs output of each record respectively).


*   Interface details: Type: Method
Name: NewLogsDecoder
Location: extension/encoding/awslogsencodingextension/internal/unmarshaler/cloudtraillog/unmarshaler.go
Signature: NewLogsDecoder(reader io.Reader, options ...encoding.DecoderOption) (encoding.LogsDecoder, error)
Description: Returns a streaming logs decoder that detects the CloudTrail log format and processes records incrementally. Supports S3 Records, CloudWatch subscription filter, and digest formats. For S3 Records, the offset tracks record count and records can be skipped via WithOffset. For CloudWatch and digest formats, a non-zero offset causes immediate EOF and the offset reflects total bytes consumed.

Type: Interface
Name: LogsDecoder
Location: extension/encoding/ (package: encoding)
Description: Interface for incremental log decoding with position tracking.
Signature:
  DecodeLogs() (plog.Logs, error)
  Offset() int64

Type: Function
Name: WithFlushItems
Location: extension/encoding/ (package: encoding)
Signature: WithFlushItems(n int) DecoderOption
Description: Returns a DecoderOption that configures the maximum number of log records to include in each batch returned by DecodeLogs(). A value of 1 causes each call to return exactly one record.

Type: Function
Name: WithOffset
Location: extension/encoding/ (package: encoding)
Signature: WithOffset(offset int64) DecoderOption
Description: Returns a DecoderOption that sets the starting position. For S3 Records format, this is the number of records to skip before beginning to decode. For CloudWatch and digest formats, a non-zero offset triggers immediate EOF behavior.

Type: Type
Name: DecoderOption
Location: extension/encoding/ (package: encoding)
Description: A function type used to configure decoder behavior, passed as variadic options to NewLogsDecoder.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.