## Description

The doctl CLI tool supports managing reserved IPv4 addresses (create, list, get, delete, assign to a server, unassign), but it has no equivalent support for reserved IPv6 addresses. Users who want to reserve static IPv6 addresses, assign them to servers, or manage them via the CLI currently have no way to do so without using the web interface or making raw API calls.

## Expected Behavior

- Users should be able to create a reserved IPv6 address in a specified region.
- Users should be able to list all reserved IPv6 addresses on their account.
- Users should be able to retrieve details about a specific reserved IPv6 address.
- Users should be able to permanently delete a reserved IPv6 address.
- Users should be able to assign a reserved IPv6 address to a specific server.
- Users should be able to unassign a reserved IPv6 address from a server.
- All commands should output a tabular view showing the IPv6 address, region, and associated server ID and name (when applicable).
- The create command should require a region to be specified and fail clearly if none is provided.

## Why This Matters

Reserved IPv6 addresses are important for high-availability setups, server migrations, and other use cases that require stable, movable IP addresses. Without CLI support, operators cannot script or automate these workflows. Bringing IPv6 reserved address management to feature parity with IPv4 allows users to manage their full networking setup through a single tool.
