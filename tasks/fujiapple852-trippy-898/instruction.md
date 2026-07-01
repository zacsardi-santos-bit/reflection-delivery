Refactor the tracing library's public API to improve usability by renaming types for brevity and implementing builder patterns for configuration objects. Ensure all existing functionality remains intact while introducing default values and equality comparison for configuration types.

*   Rename and re-export types:
    *   `TracerConfig` to `Config` in `crate::tracing`.
    *   `TracerChannelConfig` to `ChannelConfig` in `crate::tracing`, removing the `addr_family` field.
    *   `TracerProtocol` to `Protocol` with variants `Icmp`, `Udp`, `Tcp` in `crate::tracing`.
    *   `TracerAddrFamily` to `AddrFamily` with variants `Ipv4`, `Ipv6` in `crate::tracing`.

*   Implement builder patterns:
    *   `ConfigBuilder` in `src/tracing/config.rs`, re-exported from `crate::tracing`.
        *   Construct via `ConfigBuilder::new(trace_identifier: TraceId, target_addr: IpAddr) -> Self`.
        *   Provide chainable setters: `protocol`, `max_rounds`, `first_ttl`, `max_ttl`, `grace_duration`, `max_inflight`, `initial_sequence`, `multipath_strategy`, `port_direction`, `min_round_duration`, `max_round_duration`.
        *   Implement `build(self) -> Config` producing a `Config` with default values unless overridden.
    *   `ChannelConfigBuilder` in `src/tracing/config.rs`, re-exported from `crate::tracing`.
        *   Construct via `ChannelConfigBuilder::new(source_addr: IpAddr, target_addr: IpAddr) -> Self`.
        *   Provide chainable setters: `protocol`, `privilege_mode`, `multipath_strategy`, `packet_size`, `payload_pattern`, `tos`, `icmp_extension_mode`, `read_timeout`, `tcp_connect_timeout`.
        *   Implement `build(self) -> ChannelConfig` producing a `ChannelConfig` with default values unless overridden.

*   Define new enum:
    *   `IcmpExtensionParseMode` with variants `Disabled`, `Enabled` in `crate::tracing`.
    *   Implement `is_enabled(self) -> bool` for `IcmpExtensionParseMode`.

*   Create a `defaults` submodule in `src/tracing/config.rs`, re-exported from `crate::tracing`, containing:
    *   `DEFAULT_PRIVILEGE_MODE: PrivilegeMode::Privileged`
    *   `DEFAULT_STRATEGY_PROTOCOL: Protocol::Icmp`
    *   `DEFAULT_ADDRESS_FAMILY: AddrFamily::Ipv4`
    *   `DEFAULT_STRATEGY_MULTIPATH: MultipathStrategy::Classic`
    *   `DEFAULT_ICMP_EXTENSION_PARSE_MODE: IcmpExtensionParseMode::Disabled`
    *   `DEFAULT_STRATEGY_MAX_INFLIGHT: 24u8`
    *   `DEFAULT_STRATEGY_FIRST_TTL: 1u8`
    *   `DEFAULT_STRATEGY_MAX_TTL: 64u8`
    *   `DEFAULT_STRATEGY_PACKET_SIZE: 84u16`
    *   `DEFAULT_STRATEGY_PAYLOAD_PATTERN: 0u8`
    *   `DEFAULT_STRATEGY_TOS: 0u8`
    *   `DEFAULT_STRATEGY_INITIAL_SEQUENCE: 33000u16`
    *   `DEFAULT_STRATEGY_MIN_ROUND_DURATION: 1000ms`
    *   `DEFAULT_STRATEGY_MAX_ROUND_DURATION: 1000ms`
    *   `DEFAULT_STRATEGY_READ_TIMEOUT: 10ms`
    *   `DEFAULT_STRATEGY_GRACE_DURATION: 100ms`
    *   `DEFAULT_STRATEGY_TCP_CONNECT_TIMEOUT: 1000ms`

*   Ensure `Config` and `ChannelConfig` implement `Default` and `PartialEq`.

*   Validate specific builder configurations:
    *   `ChannelConfigBuilder` must correctly configure fields when all setters are used.
    *   `ConfigBuilder` must correctly configure fields when all setters are used.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.