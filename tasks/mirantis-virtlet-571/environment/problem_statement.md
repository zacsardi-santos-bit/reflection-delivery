## Description

Currently, image download and pull operations in virtlet cannot be cancelled once started. There is no mechanism to pass a cancellation signal into the download or pull path, which means that if a caller wants to abort an in-progress image download (e.g., due to a timeout, a user request to stop, or a context deadline), the operation will continue running until it completes or fails on its own.

## Expected Behavior

- Image download operations should accept a cancellation signal so that callers can interrupt them mid-flight.
- When a cancellation signal is sent during an active download, the download should stop and return an error indicating that the operation was cancelled.
- Image pull operations should also accept and propagate cancellation signals to the underlying download layer.
- When a cancellation is triggered during a pull, the pull operation should return an error indicating cancellation.

## Additional Cleanup

- Shared TLS certificate generation utilities used across multiple test packages should be extracted into a common test utility package so they are not duplicated in each test file.

## Why This Matters

Without cancellation support, the system cannot cleanly abort long-running image downloads, which can lead to resource waste and unresponsive behavior when, for example, a request is terminated by the container runtime but the image download continues running in the background. Adding cancellation support makes the image subsystem more robust and better integrated with the broader request lifecycle management.
