## Description

We need to add a new node reward command to the ioctl command-line tool that lets users query reward information from the IoTeX blockchain's rewarding protocol.

The command should support two modes of operation:

1. **Pool rewards query (no arguments)**: When run without arguments, the command should display the overall reward pool status by querying both the available balance and total balance from the rewarding protocol.

2. **Address-specific rewards query (with address/alias argument)**: When provided with an address or alias as an argument, the command should resolve that address and display the unclaimed rewards for that specific delegate.

## Expected Behavior

- Running the command without arguments should query the rewarding protocol for available balance and total balance
- Running the command with an address or alias argument should resolve the address and query the unclaimed balance for that address
- The command should integrate with the existing ioctl client infrastructure and use the API service client for making blockchain state queries

## Why This Matters

Node operators and delegates need a convenient way to check their reward status without making complex API queries manually. Additionally, the client interface needs a method to resolve an address string (which may be an alias or a direct address) to a canonical address, so that commands can accept either form of input.
