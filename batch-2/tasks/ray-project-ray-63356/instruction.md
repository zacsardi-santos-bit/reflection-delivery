I'm working on the Ray Serve HAProxy integration and need to add a metrics collection layer for the ingress request router.

*   ParsedMetrics must be a dataclass (or equivalent equality-comparable structure) with exactly these fields: app (Optional[str]), intended_server (Optional[str]), actual_server (Optional[str]), router_latency_us (Optional[int]), body_truncated_full_length (Optional[int]), via_router (bool), and failed (Optional[str]).

*   HAProxyMetricsCollector.parse_line must accept raw bytes representing an RFC 5424 syslog datagram and return a ParsedMetrics when the datagram contains a structured-data section with the SD-ID 'serve@1', or return None otherwise. It must return None (not raise) for empty input, input with no SD section, input with a different SD-ID, and binary-garbage input.

*   parse_line must map each field from the SD key-value pairs as follows: empty string for app, intended_server, actual_server, router_latency_us, body_truncated_full_length, or failed maps to None; the value '1' for via_router maps to True, any other value (including empty string) maps to False; a non-integer string value for router_latency_us maps to None rather than raising an exception.

*   HAProxyMetricsCollector.record must perform no metric operations when via_router is False and failed is None.

*   HAProxyMetricsCollector.record must observe latency_histogram with tags {'application': app_or_unknown, 'outcome': 'success'} and a value equal to router_latency_us divided by 1000.0 (converting microseconds to milliseconds) on the success path (via_router=True, failed=None).

*   HAProxyMetricsCollector.record must increment truncated_bodies_counter with tags {'application': app_or_unknown} (value 1.0) when body_truncated_full_length is not None.

*   HAProxyMetricsCollector.record must increment replica_mismatches_counter with tags {'application': app_or_unknown} (value 1.0) only when both intended_server and actual_server are non-None, actual_server is not '<NOSRV>', and intended_server differs from actual_server. It must NOT increment when they are equal, when actual_server is '<NOSRV>', or when either value is None.

*   HAProxyMetricsCollector.record must increment failures_counter with tags {'application': app_or_unknown, 'reason': failed} (value 1.0) when failed is not None.

*   HAProxyMetricsCollector.record must observe latency_histogram with tags {'application': app_or_unknown, 'outcome': 'failure'} and the converted millisecond value when a failure event has a non-None router_latency_us. When router_latency_us is None on a failure path, the histogram must not be updated.

*   HAProxyMetricsCollector.record must increment requests_counter with tags {'application': app_or_unknown} (value 1.0) for every routed event — both successes (via_router=True) and failures (failed is not None). It must NOT increment requests_counter when the event is neither routed nor failed (via_router=False and failed=None).

*   When the app field of ParsedMetrics is None, record must use the string 'unknown' as the application tag value in all metric calls.

*   HAProxyMetricsCollector must expose the metric objects as instance attributes named exactly: truncated_bodies_counter, latency_histogram, replica_mismatches_counter, failures_counter, and requests_counter. These attributes must support .inc(value, tags) and .observe(value, tags) interfaces respectively.

*   _DatagramHandler must accept a HAProxyMetricsCollector in its constructor and implement datagram_received(data, addr) such that it calls parse_line on the bytes and, if a ParsedMetrics is returned, calls record(). It must swallow all errors silently, never raising out of datagram_received.

*   HAProxyMetricsCollector.bind_and_attach must create a Unix datagram socket at the specified path. If a file already exists at that path, it must be removed before binding. After binding, the socket file must exist on disk. Datagrams sent to the socket must be processed by the _DatagramHandler and result in metric updates.

*   HAProxyMetricsCollector.close must close the asyncio transport (if one was created by bind_and_attach) and unlink the socket file from disk. It must be safe to call before bind_and_attach is ever called, and must be idempotent (calling it multiple times must not raise).

*   HAProxyConfig must accept two new constructor parameters: ingress_request_router_metrics_enabled (bool) and metrics_socket_path (str).

*   When HAProxyConfig.ingress_request_router_metrics_enabled is True, HAProxyApi._generate_config_file_internal must produce a config file that contains all of the following strings: 'log-format-sd', '[serve@1', 'format rfc5424', and 'router_latency_us'. When False, none of those strings may appear in the rendered config.

*   When HAProxyConfig.ingress_request_router_metrics_enabled is True, HAProxyApi._write_ingress_request_router_lua must produce a Lua script containing all of: 'core.now()', 'ingress_request_router_latency_us', and 'ingress_request_router_truncated_full_length'. When False, none of those strings may appear in the rendered Lua.


*   Interface details: ## New Module: `ray/serve/_private/haproxy_metrics.py`

---

Type: Class
Name: ParsedMetrics
Location: python/ray/serve/_private/haproxy_metrics.py
Description: Dataclass holding the structured fields extracted from one HAProxy RFC 5424 syslog line. All optional fields default to None when the corresponding log value was empty or absent.
Fields:
  app: Optional[str]
  intended_server: Optional[str]
  actual_server: Optional[str]
  router_latency_us: Optional[int]
  body_truncated_full_length: Optional[int]
  via_router: bool
  failed: Optional[str]

---

Type: Class
Name: HAProxyMetricsCollector
Location: python/ray/serve/_private/haproxy_metrics.py
Description: Parses RFC 5424 syslog datagrams from HAProxy and records routing metrics. Manages a Unix datagram socket for receiving those datagrams.
Methods:
  __init__() -> None
    Creates metric objects as instance attributes:
      self.truncated_bodies_counter  (Counter-like: .inc(value, tags))
      self.latency_histogram         (Histogram-like: .observe(value, tags))
      self.replica_mismatches_counter (Counter-like: .inc(value, tags))
      self.failures_counter          (Counter-like: .inc(value, tags))
      self.requests_counter          (Counter-like: .inc(value, tags))

  parse_line(raw: bytes) -> Optional[ParsedMetrics]
    Class or static method. Parses a raw RFC 5424 datagram; returns a
    ParsedMetrics on success or None when the SD section is missing or
    the SD-ID is not "serve@1".

  record(parsed: ParsedMetrics) -> None
    Records metric observations for one routing event.

  async bind_and_attach(sock_path: str, loop) -> None
    Binds a Unix SOCK_DGRAM socket at sock_path, removing any stale file
    first, and attaches an asyncio datagram endpoint using _DatagramHandler.

  close() -> None
    Closes the asyncio transport (if any) and unlinks the socket file.
    Safe to call before bind_and_attach and idempotent across multiple calls.

---

Type: Class
Name: _DatagramHandler
Location: python/ray/serve/_private/haproxy_metrics.py
Description: Asyncio DatagramProtocol that feeds received bytes into a HAProxyMetricsCollector.
Methods:
  __init__(collector: HAProxyMetricsCollector) -> None

  datagram_received(data: bytes, addr) -> None
    Calls HAProxyMetricsCollector.parse_line on data; if a ParsedMetrics is
    returned, calls collector.record(). Swallows all errors without raising.

---

## Existing Module Changes: `ray/serve/_private/haproxy.py`

Type: Class
Name: HAProxyConfig
Location: python/ray/serve/_private/haproxy.py
Description: Existing configuration dataclass for the HAProxy integration. Gains two new constructor parameters for the metrics feature.
New fields:
  ingress_request_router_metrics_enabled: bool
  metrics_socket_path: str

---

Type: Class
Name: HAProxyApi
Location: python/ray/serve/_private/haproxy.py
Description: Existing class that generates HAProxy config files and Lua scripts. The two existing methods listed below gain conditional metrics-rendering logic based on HAProxyConfig.ingress_request_router_metrics_enabled.
Methods (existing, with new behaviour):
  _generate_config_file_internal() -> None
    When ingress_request_router_metrics_enabled is True, the rendered config
    must contain the strings "log-format-sd", "[serve@1", "format rfc5424",
    and "router_latency_us". When False, none of those strings may be present.

  _write_ingress_request_router_lua(backends: list) -> Optional[str]
    When ingress_request_router_metrics_enabled is True, the rendered Lua
    source must contain "core.now()", "ingress_request_router_latency_us",
    and "ingress_request_router_truncated_full_length". When False, none of
    those strings may be present.

Also used in tests (already exist in haproxy.py, no changes needed):
  BackendConfig
  ServerConfig


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.