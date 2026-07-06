## Feature Request: Support for Demo Recordings in the Integration Test Framework

### Description

We have a powerful integration test framework that drives the application in a scripted way and verifies its behavior. It would be great to reuse this same infrastructure to produce animated demo recordings that can appear in the documentation and README.

The challenge is that demo scenarios have different requirements from automated tests:
- They may include artificial pauses or timing delays that would make CI runs slow or flaky
- They need to display captions visible in the recording to explain what is happening
- They should be excluded automatically from regular test runs

### Proposed Solution

Add a way to mark an integration test as a "demo." When a test is flagged as a demo:
- The automated test runner should skip it entirely (since demos are recorded manually, not verified by CI)
- The test driver should support setting a visible caption and caption prefix that appears during recording

Once the infrastructure is in place, add an initial set of demos covering common workflows:
- Making a commit and pushing to a remote
- Cherry-picking commits between branches
- Interactive rebasing
- Git bisect

### Expected Behavior

- Developers can write a demo scenario exactly like a normal integration test, but with a flag indicating it is a demo
- Running the normal test suite ignores demo scenarios automatically
- The test driver provides methods to set caption text (both a prefix and a body) during scenario execution
- Shell setup supports creating a realistic-looking history using randomized commit messages
- Demo scenarios can be manually run in slow mode to record them with external tools

### Why This Matters

The existing integration test framework is already set up to drive the application reliably. Reusing it for demos means we get reproducible, updatable recordings with minimal effort. When the UI changes, we can regenerate the demos rather than re-record them manually.
