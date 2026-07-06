## Description

The BPF-based IPv6 Neighbor Discovery Protocol (NDP) handler in the network device datapath has two correctness bugs that affect how Neighbor Solicitation (NS) packets are processed and how Neighbor Advertisement (NA) responses are generated.

**Bug 1: NA responses have wrong source IP**

When the handler generates a Neighbor Advertisement for a pod endpoint, it uses the router's IP address as the NA source address. According to the NDP protocol, the NA source IP should be the IP address of the target endpoint being resolved — not the router IP. This means any host receiving the NA will cache the wrong IP-to-MAC binding.

**Bug 2: NS packets without a LL source option are not handled**

RFC 4861 states that the Source Link-Layer Address option in NS messages is optional for unicast reachability checks, and RFC 4862 (Duplicate Address Detection) explicitly requires that this option be absent. Currently, when an NS arrives without this option, the handler fails to generate a proper response.

## Expected Behavior

- Neighbor Solicitations targeting Pod IPs should be answered with a Neighbor Advertisement whose source IP is the target pod's IP address (not the router IP)
- NS packets without the optional Source Link-Layer Address field should be handled gracefully and still receive a correct NA response
- Both unicast and solicited-node multicast NS addressing modes should work correctly
- Neighbor Solicitations targeting the node's own IP should continue to pass through to the kernel stack unmodified

## Why This Matters

These bugs cause incorrect ARP/NDP cache entries on neighboring hosts and drop legitimate NS packets from sources performing Duplicate Address Detection. Fixing them is necessary for correct IPv6 neighbor resolution behavior in the Cilium BPF datapath.
