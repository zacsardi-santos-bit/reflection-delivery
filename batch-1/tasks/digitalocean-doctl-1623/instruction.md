Implement support for managing reserved IPv6 addresses in the doctl CLI tool. Add subcommands for creating, listing, retrieving, deleting, assigning, and unassigning IPv6 addresses, ensuring feature parity with existing IPv4 address management.

*   Update the `ReservedIPv6sService` interface in `do/reserved_ipv6s.go`:
    *   Define methods: `List() (ReservedIPv6s, error)`, `Get(ip string) (*ReservedIPv6, error)`, `Create(ficr *godo.ReservedIPV6CreateRequest) (*ReservedIPv6, error)`, `Delete(ip string) error`.
*   Define `ReservedIPv6` struct and `ReservedIPv6s` alias in `do/reserved_ipv6s.go`:
    *   `ReservedIPv6` wraps `*godo.ReservedIPV6`.
    *   `ReservedIPv6s` is a slice of `ReservedIPv6`.
*   Update `ReservedIPv6ActionsService` interface in `do/reserved_ipv6_actions.go`:
    *   Define methods: `Assign(ip string, dropletID int) (*Action, error)`, `Unassign(ip string) (*Action, error)`.
*   Create mock types in `do/mocks/`:
    *   `MockReservedIPv6sService` and `MockReservedIPv6ActionsService` using constructors `NewMockReservedIPv6sService(ctrl)` and `NewMockReservedIPv6ActionsService(ctrl)`.
*   Modify `CmdConfig` struct in `commands/command_config.go`:
    *   Add fields: `ReservedIPv6s func() do.ReservedIPv6sService`, `ReservedIPv6Actions func() do.ReservedIPv6ActionsService`.
    *   Initialize these fields in `NewCmdConfig`.
*   Implement `ReservedIPv6()` in `commands/reserved_ipv6s.go`:
    *   Return a `*Command` with subcommands: `create` (alias `c`), `delete` (aliases `d`, `rm`), `get` (alias `g`), `list` (alias `ls`).
*   Implement `ReservedIPv6Action()` in `commands/reserved_ipv6_actions.go`:
    *   Return a `*Command` with subcommands: `assign`, `unassign`.
*   Implement command handlers in `commands/reserved_ipv6s.go`:
    *   `RunReservedIPv6Create`: Read region slug flag, return error if empty, call `ReservedIPv6sService.Create`.
    *   `RunReservedIPv6Get`: Accept IPv6 address, call `ReservedIPv6sService.Get`.
    *   `RunReservedIPv6Delete`: Accept IPv6 address and force flag, call `ReservedIPv6sService.Delete` if force is true.
    *   `RunReservedIPv6List`: Call `ReservedIPv6sService.List` and display results.
*   Implement command handlers in `commands/reserved_ipv6_actions.go`:
    *   `RunReservedIPv6ActionsAssign`: Accept IPv6 address and droplet ID, call `ReservedIPv6ActionsService.Assign`.
    *   `RunReservedIPv6ActionsUnassign`: Accept IPv6 address, call `ReservedIPv6ActionsService.Unassign`.
*   Update displayer in `commands/displayers/reserved_ipv6.go`:
    *   Use `ReservedIPv6` struct with `ReservedIPv6s do.ReservedIPv6s`.
    *   Implement `Displayable` interface with columns: IP, Region, DropletID, DropletName.
*   Register CLI commands in `commands/doit.go`:
    *   Register `compute reserved-ipv6` and `compute reserved-ipv6-action` under the compute command group.
*   Fix JSON parsing in `vendor/github.com/digitalocean/godo/reserved_ipv6.go`:
    *   Implement `reservedIPV6Root` and `reservedIPV6sRoot` structs for response parsing.
    *   Update Get and Create operations to decode into `reservedIPV6Root`.
    *   Update List operation to use `reservedIPV6sRoot` with `ReservedIPV6s` field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.