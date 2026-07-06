I'd like to add a command to the ioctl command-line tool that lets users query reward information from the IoTeX blockchain. Currently there's no convenient way to check the reward pool status or see how many unclaimed rewards a specific delegate has accumulated.

The command should work in two ways: when run without any arguments, it should show the overall reward pool information by fetching both the available and total reward balances. When given an account address or alias as an argument, it should resolve that to a canonical address and then show the unclaimed rewards for that specific account.

This would help node operators and delegates easily check their reward status. The implementation should also extend the existing client interface with the ability to resolve an address input — accepting either a direct address or a configured alias — and return the canonical address string.
