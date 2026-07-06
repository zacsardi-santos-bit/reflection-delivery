I'm working with MLflow tracing and I need a way to record relationships between spans that live in different traces.

*   A new Link entity class must be implemented in mlflow/entities/link.py with instance attributes trace_id (str), span_id (str), and attributes (optional dict, default None). The constructor must accept trace_id, span_id, and an optional attributes keyword argument.

*   Link.to_dict() must return a dictionary with exactly the keys 'trace_id', 'span_id', and 'attributes' mapping to the corresponding instance values.

*   Link.from_dict(data) must be a classmethod that constructs a Link from a dict. If 'attributes' is absent from the dict, the resulting Link's attributes must be None.

*   Link must be importable from mlflow.entities (i.e., added to the mlflow.entities package's public API).

*   The Span class must expose a 'links' property that returns a deep copy of stored Link objects. Mutations to the returned list or to its Link objects' attributes must not affect the Span's internal state.

*   Span.add_link(link) must validate the link before storing it. The trace_id must start with 'tr-' followed by a valid hexadecimal string; the span_id must be a valid hexadecimal string of at most 16 characters (8 bytes). Any violation must raise an MlflowException with a message matching 'Invalid link'.

*   Span.add_link(link) must clone the link on insert so that subsequent mutations to the caller's original Link object do not affect the stored link.

*   Span.add_link(link) must forward the link to the underlying OpenTelemetry span object, converting the trace_id hex portion and span_id hex to their integer representations.

*   Span.to_dict() must include a 'links' key containing a list of link dictionaries (one per Link via Link.to_dict()). When there are no links the value must be an empty list [].

*   Span.from_dict(data) must reconstruct the span's links by reading the 'links' key from the input dict and constructing Link objects via Link.from_dict() for each entry.

*   Span.from_otel_proto(otel_proto) without a location_id must populate span.links from the OTel proto span's links field. Each link's trace_id bytes must be converted to a string in the format 'tr-' followed by the lowercase hex representation of the 16 bytes; span_id bytes must be converted to lowercase hex. Attributes from the proto must be deserialized and stored on the Link.

*   Span.from_otel_proto(otel_proto, location_id=...) when a location_id is provided (v4 Unity Catalog traces) must skip all links — the resulting span.links must be an empty list.

*   For spans whose trace_id starts with the v4 trace ID prefix (i.e., Unity Catalog format traces), span.links must return an empty list regardless of any links present on the underlying OTel span.

*   Span.to_otel_proto() must round-trip links: the output proto's links must contain the original trace_id and span_id as bytes (reconstructed from the hex strings stored in the Link objects), preserving the exact byte sequences from the original proto.

*   Span.to_immutable_span() must deep copy the live span's links so that subsequent mutations to the live span's internal _links list do not affect the immutable span's links.

*   The SQLAlchemy tracking store must persist link data attached to spans. After calling log_spans with spans that have links, a subsequent get_trace call must return those spans with their links intact, preserving trace_id, span_id, and attributes exactly.


*   Interface details: Type: Class
Name: Link
Location: mlflow/entities/link.py
Description: Represents a cross-trace link from one span to a span in another trace. Stores the target trace ID, target span ID, and optional metadata attributes.
Signature:
  __init__(self, trace_id: str, span_id: str, attributes: Optional[dict] = None)
  to_dict(self) -> dict  # Returns {"trace_id": ..., "span_id": ..., "attributes": ...}
  from_dict(cls, data: dict) -> "Link"  # classmethod; attributes defaults to None if absent from data

Type: Class
Name: Link
Location: mlflow/entities/__init__.py
Description: Link must be exported from the mlflow.entities package (added to __init__.py so that `from mlflow.entities import Link` works).

Type: Method
Name: links (property)
Location: mlflow/entities/span.py  (on the Span class)
Description: Returns a deep copy of the list of Link objects attached to this span. Mutations to the returned list or the Link objects within it must not affect the span's internal state.
Signature: @property  def links(self) -> list[Link]

Type: Method
Name: add_link
Location: mlflow/entities/span.py  (on the Span class, specifically on the live/mutable span variant)
Description: Validates and attaches a Link to this span. Raises MlflowException with a message matching "Invalid link" if the trace_id is not in the format "tr-<hex>" or if the span_id is not a valid hex string of at most 16 characters. Clones the link before storing it. Also forwards the link to the underlying OpenTelemetry span.
Signature: def add_link(self, link: Link) -> None

Type: Method
Name: from_otel_proto
Location: mlflow/entities/span.py  (classmethod on Span)
Description: Must populate span.links from the OTel proto span's links field when no location_id is provided. Each link's trace_id bytes are converted to "tr-" + lowercase hex; span_id bytes converted to lowercase hex. When location_id is provided (v4 Unity Catalog traces), links are skipped and span.links is empty.
Signature: def from_otel_proto(cls, otel_proto, location_id: Optional[str] = None) -> "Span"

Type: Method
Name: to_otel_proto
Location: mlflow/entities/span.py  (on Span)
Description: Must include links in the output proto, reconstructing trace_id and span_id bytes from the stored hex strings.
Signature: def to_otel_proto(self) -> OTelProtoSpan

Type: Method
Name: to_dict
Location: mlflow/entities/span.py  (on Span)
Description: Must include a "links" key in the returned dictionary. The value is a list of dicts (one per Link via Link.to_dict()), or an empty list if there are no links.
Signature: def to_dict(self) -> dict

Type: Method
Name: from_dict
Location: mlflow/entities/span.py  (classmethod on Span)
Description: Must read the "links" key from the input dict and reconstruct Link objects via Link.from_dict() for each entry.
Signature: def from_dict(cls, data: dict) -> "Span"

Type: Method
Name: to_immutable_span
Location: mlflow/entities/span.py  (on the live/mutable Span class)
Description: Must deep copy the live span's links when creating the immutable span, so that subsequent mutations to the live span's _links do not affect the immutable span.
Signature: def to_immutable_span(self) -> "Span"

Type: Attribute
Name: _links
Location: mlflow/entities/span.py  (internal attribute on Span)
Description: Internal list of Link objects. Tests directly assign to span._links in the SQLAlchemy store test helper. Must be a plain Python list.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.