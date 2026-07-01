I'm working on improving how Kafka Connect handles broker coordinator failures in distributed mode. Right now, when the broker goes offline temporarily, workers block indefinitely trying to reconnect to the group coordinator. This causes tasks to stay in a bad state even after the broker comes back, requiring manual intervention to recover.

I'd like to fix this so that workers detect coordinator unavailability within a bounded timeout and proactively revoke their assignments rather than waiting forever. Then, when the broker comes back online on the same ports, the workers should automatically rejoin the group and all connector tasks should resume running on their own.

I also want to make sure that failed connector tasks can be restarted through the REST interface. If a task fails because of a bad configuration (like a wrong broker address in its overrides), and then the connector is reconfigured with valid settings, posting a restart to the task's REST endpoint should bring it back to a running state.

Finally, the incremental cooperative rebalance assignor should incorporate the current group membership identity — the generation and member information — when performing task assignments. This makes it possible to correlate each assignment with the rebalance it belongs to.

There's also test infrastructure work needed: the embedded Kafka cluster used in integration tests should support stopping and restarting only the Kafka brokers (without affecting ZooKeeper), and should be able to restart brokers on the same ports they previously used so that existing connections can recover. The embedded Connect cluster should support sending HTTP POST requests to the REST API endpoints.
