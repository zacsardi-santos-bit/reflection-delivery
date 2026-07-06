I'm working on improving the testability of the distributed transaction commit handling code in Apache Geode. Right now, there are two areas that are hard to unit test:

First, the logic for tracking whether a transaction is currently being processed by a remote member is buried inside a large method. I need to be able to check this condition independently — including handling the case where no message exists at all — and also verify what happens when a transaction is found in history versus found to be currently in progress versus neither.

Second, the behavior that fires when the originating cluster member departs — specifically, creating and sending a query to check commit status and then waiting for replies — is similarly entangled with infrastructure that requires a running cluster. I'd like to extract this into a form where the individual steps (creating the reply processor, creating the query message, sending it, waiting for replies) can each be overridden and verified in isolation.

Could you refactor the relevant classes so that these behaviors are exposed as individually overridable methods, with clear and testable logic for each case?
