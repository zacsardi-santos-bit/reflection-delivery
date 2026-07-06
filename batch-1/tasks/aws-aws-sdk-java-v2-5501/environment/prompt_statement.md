I'm using the DynamoDB Enhanced client to perform transactional writes, and I'd like to be able to see how much capacity was consumed by the operation and get item collection metrics back. Right now, the transactional write operation just returns nothing, so even if I configure the request to report consumed capacity, there's nowhere to receive that data.

I need a new variant of the transactional write method on both the synchronous and asynchronous enhanced client that returns a response object with consumed capacity and item collection metrics. The request builder also needs to let me specify that I want consumed capacity or collection metrics returned by DynamoDB.

The response object should have proper builder support and correct equality semantics. When capacity reporting is enabled on the request and the operation runs successfully, the response should contain the consumed capacity list and item collection metrics map from DynamoDB.
