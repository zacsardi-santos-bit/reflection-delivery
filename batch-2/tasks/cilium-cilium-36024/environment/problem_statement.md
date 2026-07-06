I'm chasing down some IPv6 Neighbor Discovery bugs in the BPF datapath, the code that handles NS/NA in the network device path (Cilium style). Couple of correctness issues plus some test scaffolding that's missing so things won't even compile yet.

First bug: when the handler builds a Neighbor Advertisement in reply to a Neighbor Solicitation for a pod IP, it's stuffing the router IP into the NA source address. That's wrong, the NA source IP has to be the IP of the target endpoint that was actually solicited, otherwise any host that gets the NA caches a bogus IP-to-MAC binding. So I need the source to reflect the real target.

Second, the handler assumes every NS carries a Source Link-Layer Address option, but per RFC 4861 that option's optional for unicast reachability checks, and RFC 4862 actually requires it be absent during Duplicate Address Detection. Right now an NS without it just fails, so DAD packets get dropped. It needs to handle the option-present and option-absent cases gracefully and still emit a proper NA with the target link-layer address option filled in.

Third, both addressing modes need to work, plain unicast NS and solicited-node multicast NS (the multicast form is how initial address resolution normally happens), oh and NS targeting the node's own IP should still pass through to the kernel stack untouched.

Also there's some infra to add for the new BPF tests: define a constant for the size of a Neighbor Discovery option (8 bytes) in the ICMPv6 header file, and add two test helper constants in the packet-generation header, one for the IPv6 multicast MAC prefix and one for the IPv6 multicast address prefix. Without those the tests won't build.
