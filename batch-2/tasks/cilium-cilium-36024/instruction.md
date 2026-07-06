Fix the IPv6 Neighbor Discovery Protocol handling in the BPF network device datapath by addressing two bugs related to Neighbor Solicitation and Advertisement processing. Implement missing infrastructure for compiling BPF tests.

*   Define constants:
    *   Define `ICMP6_ND_OPT_LEN` as `8` in `bpf/lib/icmp6.h`.
    *   Define `mac_v6mcast_base` as a `volatile const __u8` array `{0x33, 0x33, 0x00, 0x00, 0x00, 0x00}` in `bpf/tests/pktgen.h`.
    *   Define `v6_mcast_base` as a `volatile const __u8` array `{0xff, 0x02, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0x01, 0xFF, 0, 0, 0}` in `bpf/tests/pktgen.h`.

*   Update the BPF NDP handler:
    *   For Neighbor Solicitations targeting a Pod IP:
        *   Respond with `CTX_ACT_REDIRECT`.
        *   Generate a Neighbor Advertisement with:
            *   Source MAC as the node interface MAC.
            *   Destination MAC as the NS sender's MAC.
            *   Source IP as the target IP from the NS body.
            *   Destination IP as the NS sender's IP.
            *   ICMPv6 type as `ICMP6_NA_MSG_TYPE`.
            *   Include a target link-layer address option (type=0x2, length=0x1) with the node interface MAC.
    *   For Neighbor Solicitations targeting a Node IP:
        *   Pass the packet through to the stack unmodified with status code `CTX_ACT_OK`.
    *   Ensure both unicast and solicited-node multicast Neighbor Solicitations are handled correctly for Pod and Node IPs.
    *   Handle Neighbor Solicitations without a source link-layer address option without error, ensuring the response for Pod IPs includes the target link-layer address option.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.