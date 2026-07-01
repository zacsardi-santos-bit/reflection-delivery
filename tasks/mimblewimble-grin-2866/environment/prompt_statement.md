I'm working on a blockchain node codebase that supports both a production network and a test network. The protocol has scheduled hard forks that change the required header version at certain block heights. Right now, the header version validation uses a single hard-coded schedule regardless of which network the node is running on, which means the test network can't have its own independent hard fork timing.

I need the header version validation to be network-aware — on the production network it should use the mainnet fork heights, and on the test network it should use an earlier, separate set of fork heights. The test network's first hard fork should occur at a dedicated block height that's different from (and earlier than) the mainnet schedule.

There also seems to be a bug where the second protocol version isn't accepted at the correct activation height on mainnet — it should become valid exactly at the first fork boundary, not just after it.

A constant for the test network's first hard fork block height should be defined and exported from the consensus module so it can be referenced elsewhere. The network-type detection already exists in the global module, so the validation logic just needs to consult it when deciding which fork schedule to apply. Neither network should yet have a third hard fork active at the heights we're currently concerned with.
