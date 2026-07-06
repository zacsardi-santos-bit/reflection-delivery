I'm working on the DNS proxy in cilium and I'd like to refactor how DNS message data is passed through the pipeline.

*   A new exported struct MsgDetails must be defined in the pkg/fqdn/dnsproxy package with the following fields: QName (string), QTypes ([]uint16), Response (bool), ResponseIPs ([]netip.Addr), CNAMEs ([]string), AnswerTypes ([]uint16), TTL (uint32), RCode (int).

*   ExtractRequestMsgDetails must accept a *dns.Msg and return (*MsgDetails, error). It must return an error when the message has no questions (empty question section).

*   For valid DNS request messages, ExtractRequestMsgDetails must return a MsgDetails with QName set to the FQDN (trailing dot included), QTypes set to the list of question types, and Response set to false.

*   ExtractRequestMsgDetails must never populate response-specific fields for request messages: ResponseIPs and CNAMEs and AnswerTypes must all be nil, and TTL must be 0, even if the message contains a spoofed answer section.

*   ExtractResponseMsgDetails must accept a *dns.Msg and return (*MsgDetails, error). It replaces the previous ExtractMsgDetails function. The returned MsgDetails must have TTL (uint32) and CNAMEs ([]string) populated from the DNS response.

*   The NotifyOnDNSMsg callback type used when constructing the DNS proxy must accept *MsgDetails as the DNS message parameter instead of *dns.Msg.

*   The NotifyOnDNSMsg method in pkg/fqdn/messagehandler must accept *dnsproxy.MsgDetails instead of *dns.Msg. Callers must pre-extract the MsgDetails via ExtractResponseMsgDetails before calling NotifyOnDNSMsg.

*   The NotifyOnDNSMsg method in standalone-dns-proxy/pkg/messagehandler must accept *dnsproxy.MsgDetails instead of *dns.Msg. It must return an error if the endpoint parameter is nil.

*   When NotifyOnDNSMsg in standalone-dns-proxy/pkg/messagehandler is called with an empty MsgDetails (zero value), it must succeed and produce a pb.FQDNMapping with: Fqdn empty string, Ttl 0, ResponseCode 0, SourceIp set from the epIPPort host portion, SourceIdentity from the endpoint's numeric security identity.

*   When NotifyOnDNSMsg in standalone-dns-proxy/pkg/messagehandler succeeds with a populated MsgDetails, the resulting pb.FQDNMapping must have: Fqdn from MsgDetails.QName, RecordIp entries from MsgDetails.ResponseIPs (each converted to its string/byte representation), Ttl from MsgDetails.TTL, SourceIp from the host portion of epIPPort, SourceIdentity from ep.SecurityIdentity.ID, ResponseCode from MsgDetails.RCode.


*   Interface details: Type: Struct
Name: MsgDetails
Location: pkg/fqdn/dnsproxy/
Description: Pre-parsed DNS message details extracted from a raw dns.Msg. Used to pass structured DNS data through the proxy pipeline instead of raw protocol messages. Replaces passing *dns.Msg to NotifyOnDNSMsg callbacks.
Fields:
  QName       string
  QTypes      []uint16
  Response    bool
  ResponseIPs []netip.Addr
  CNAMEs      []string
  AnswerTypes []uint16
  TTL         uint32
  RCode       int

---

Type: Function
Name: ExtractRequestMsgDetails
Location: pkg/fqdn/dnsproxy/
Signature: ExtractRequestMsgDetails(msg *dns.Msg) (*MsgDetails, error)
Description: Extracts structured details from a DNS request message. Returns an error if the message has no question section. For valid requests, returns MsgDetails with QName and QTypes populated and Response set to false. Response-specific fields (ResponseIPs, CNAMEs, AnswerTypes) must be nil and TTL must be 0. Any answer section present in a request message (e.g. spoofed answers) must be ignored — it must not populate the response fields.

---

Type: Function
Name: ExtractResponseMsgDetails
Location: pkg/fqdn/dnsproxy/
Signature: ExtractResponseMsgDetails(msg *dns.Msg) (*MsgDetails, error)
Description: Extracts structured details from a DNS response message. Replaces the old ExtractMsgDetails function which returned multiple positional values. Returns a *MsgDetails where at minimum TTL (uint32) and CNAMEs ([]string) are populated from the response, along with other response fields.

---

Type: Method
Name: NotifyOnDNSMsg
Location: pkg/fqdn/messagehandler/
Signature: NotifyOnDNSMsg(lookupTime time.Time, ep *endpoint.Endpoint, epIPPort string, serverID identity.NumericIdentity, dstAddr netip.AddrPort, details *dnsproxy.MsgDetails, protocol string, allowed bool, stat *dnsproxy.ProxyRequestContext) error
Description: Updated to accept *dnsproxy.MsgDetails instead of *dns.Msg. Pre-extracted message details are passed in by the caller (extracted via ExtractResponseMsgDetails before calling).

---

Type: Method
Name: NotifyOnDNSMsg
Location: standalone-dns-proxy/pkg/messagehandler/
Signature: NotifyOnDNSMsg(lookupTime time.Time, ep *endpoint.Endpoint, epIPPort string, serverID identity.NumericIdentity, dstAddr netip.AddrPort, details *dnsproxy.MsgDetails, protocol string, allowed bool, stat *dnsproxy.ProxyRequestContext) error
Description: Updated to accept *dnsproxy.MsgDetails instead of *dns.Msg. Must return an error if the endpoint (ep) is nil. Must succeed (return nil error) when given an empty MsgDetails{}. When successful, constructs a pb.FQDNMapping from the details fields: Fqdn from QName, RecordIp from ResponseIPs (as string bytes), Ttl from TTL, SourceIp from epIPPort (host part), SourceIdentity from ep.SecurityIdentity.ID, ResponseCode from RCode.

---

Note: The NewDNSProxy callback parameter type also changes from *dns.Msg to *MsgDetails:
Old: func(lookupTime time.Time, ep *endpoint.Endpoint, epIPPort string, serverID identity.NumericIdentity, dstAddr netip.AddrPort, msg *dns.Msg, protocol string, allowed bool, stat *ProxyRequestContext) error
New: func(lookupTime time.Time, ep *endpoint.Endpoint, epIPPort string, serverID identity.NumericIdentity, dstAddr netip.AddrPort, details *MsgDetails, protocol string, allowed bool, stat *ProxyRequestContext) error


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.