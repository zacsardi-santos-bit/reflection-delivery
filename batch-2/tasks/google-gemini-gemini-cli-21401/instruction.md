I'm working on adding SSRF (server-side request forgery) protections to a CLI tool that makes outbound HTTP requests.

*   The isAddressPrivate function must return true for standard private IPv4 ranges (10.0.0.0/8, 127.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) and false for public IPv4 addresses such as 8.8.8.8 or 93.184.216.34.

*   The isAddressPrivate function must return true for all RFC 6890 reserved and non-routable IPv4 ranges, including: 0.0.0.0/8, 100.64.0.0/10, 192.0.0.0/24, 192.0.2.0/24, 192.88.99.0/24, 198.18.0.0/15 (including 198.18.0.0, 198.18.0.1, 198.19.255.255), 198.51.100.0/24, 203.0.113.0/24, 224.0.0.0/4 (multicast), and 240.0.0.0/4.

*   The isAddressPrivate function must return true for private IPv6 addresses including loopback (::1), unique-local (fc00:: and fd00::), and link-local ranges (fe80:: through febf::).

*   The isAddressPrivate function must return true for the special addresses '0.0.0.0', '::', and the literal string 'localhost'.

*   The isAddressPrivate function must return true for link-local address 169.254.169.254.

*   The isAddressPrivate function must return true for IPv4-mapped IPv6 addresses (::ffff:x.x.x.x) where the embedded IPv4 address is private, including ::ffff:127.0.0.1, ::ffff:10.0.0.1, ::ffff:169.254.169.254, ::ffff:192.168.1.1, ::ffff:172.16.0.1, ::ffff:0.0.0.0, ::ffff:100.64.0.1, and ::ffff:a9fe:101 (169.254.1.1 in hex notation). It must return false for ::ffff:8.8.8.8.

*   The isPrivateIp function must return true for URLs whose hostname is a private IP (e.g., http://10.0.0.1/, https://127.0.0.1:8080/, http://localhost/, http://[::1]/) and false for URLs with public IPs or domain names (e.g., http://8.8.8.8/, https://google.com/).

*   The isPrivateIpAsync function must return false for invalid URLs (e.g., 'not-a-url') without throwing an error.

*   The isPrivateIpAsync function must return true for URLs with a direct private IP host without requiring DNS resolution.

*   The isPrivateIpAsync function must perform DNS resolution for domain names; it must return true if any resolved address is private (e.g., 10.0.0.1) and false if all resolved addresses are public (e.g., 8.8.8.8).

*   The isPrivateIpAsync function must throw an error with the message 'Failed to verify if URL resolves to private IP' when DNS resolution fails (fail-closed behavior).

*   The safeLookup function must filter private IP addresses from DNS lookup results, returning only non-private addresses to the callback. When the queried hostname is 'localhost', it must allow 127.0.0.1 through. When all resolved addresses are private (for non-localhost hostnames), it must call the callback with a PrivateIpError instance.

*   The safeFetch function must call the global fetch with a 'dispatcher' option (an object) to enable private network blocking. When a PrivateIpError is thrown during the fetch, it must reject with an error whose message contains 'Access to private network is blocked'.

*   The fetchWithTimeout function must abort the request after the specified timeout (in milliseconds) and throw an error with the message 'Request timed out after {N}ms'. When a PrivateIpError is encountered, it must throw an error with the message 'Access to private network is blocked: {url}' where {url} is the requested URL.

*   The PrivateIpError class must be a custom Error that can be instantiated with new PrivateIpError() and detected via instanceof PrivateIpError checks.


*   Interface details: Type: Function
Name: isAddressPrivate
Location: packages/core/src/utils/fetch.ts
Signature: isAddressPrivate(address: string) -> boolean
Description: Synchronously determines whether a given IP address string (IPv4, IPv6, or hostname like "localhost") refers to a private, reserved, or non-routable address. Returns true if the address is private/reserved, false if it is public.

Type: Function
Name: isPrivateIp
Location: packages/core/src/utils/fetch.ts
Signature: isPrivateIp(url: string) -> boolean
Description: Synchronously checks whether the hostname in a URL is a private or reserved IP address. Returns true if private, false otherwise. Does not perform DNS resolution.

Type: Function
Name: isPrivateIpAsync
Location: packages/core/src/utils/fetch.ts
Signature: isPrivateIpAsync(url: string) -> Promise<boolean>
Description: Asynchronously checks whether a URL resolves to a private IP address. For invalid URLs, returns false without throwing. For direct private IPs, returns true immediately. For domain names, performs a DNS lookup; returns true if any resolved address is private. If DNS resolution fails, throws an Error with the message "Failed to verify if URL resolves to private IP".

Type: Function
Name: safeLookup
Location: packages/core/src/utils/fetch.ts
Signature: safeLookup(hostname: string, options: dns.LookupOptions, callback: (err: Error | null, addresses: Array<{address: string; family: number}>) => void) -> void
Description: A drop-in replacement for the Node.js dns.lookup callback API that filters out private IP addresses from resolved results. When the explicitly requested hostname is "localhost", allows 127.0.0.1 through. When all resolved addresses are private, calls the callback with a PrivateIpError instance.

Type: Function
Name: safeFetch
Location: packages/core/src/utils/fetch.ts
Signature: safeFetch(url: string, ...options) -> Promise<Response>
Description: A wrapper around the global fetch that injects a dispatcher option to block private network access. If a PrivateIpError is thrown during the request, rethrows with the message "Access to private network is blocked".

Type: Function
Name: fetchWithTimeout
Location: packages/core/src/utils/fetch.ts
Signature: fetchWithTimeout(url: string, timeout: number) -> Promise<Response>
Description: Fetches a URL with a timeout in milliseconds. If the request times out, throws an error with the message "Request timed out after {N}ms". If a PrivateIpError is encountered, throws an error with the message "Access to private network is blocked: {url}".

Type: Class
Name: PrivateIpError
Location: packages/core/src/utils/fetch.ts
Description: A custom Error subclass representing an attempt to access a private or reserved network address. Can be instantiated with new PrivateIpError() and used with instanceof checks.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.