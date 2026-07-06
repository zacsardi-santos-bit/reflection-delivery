I'm working on the Anthropic integration for Home Assistant and running into several related issues with how the model's internal reasoning (thinking) information is stored and reused across conversation turns.

*   The ContentDetails dataclass must have three fields: citation_details (list of CitationDetails, default empty list), thinking_signature (str or None, default None), and redacted_thinking (str or None, default None). Its __bool__ method must return True if thinking_signature is not None, redacted_thinking is not None, or any citation detail has citations.

*   When _transform_stream processes a ThinkingBlock from the stream, it must store the signature from the SignatureDelta event in ContentDetails.thinking_signature, not create a ThinkingBlock object as the native value.

*   When _transform_stream processes a RedactedThinkingBlock from the stream, it must store the block's data in ContentDetails.redacted_thinking, not create a RedactedThinkingBlock object as the native value.

*   When _transform_stream encounters a ThinkingBlock start event and the current content_details already has a thinking_signature set (or it is the first block), it must yield the existing content_details as native and start a new ContentDetails for the new assistant turn.

*   When _transform_stream processes a web search result block (WebSearchToolResultBlock), it must reset the first_block flag to True so that a subsequent ThinkingBlock is treated as the start of a new assistant content block.

*   When _convert_content processes an AssistantContent whose native field is a ContentDetails instance with a non-None thinking_signature, it must prepend a ThinkingBlockParam (type='thinking', thinking=content.thinking_content or '', signature=content.native.thinking_signature) to the message content list.

*   When _convert_content processes an AssistantContent whose native field is a ContentDetails instance with a non-None redacted_thinking, it must prepend a RedactedThinkingBlockParam (type='redacted_thinking', data=content.native.redacted_thinking) to the message content list.

*   After _convert_content builds an assistant message content list with exactly one text block and no other block types, it must simplify the content field to a plain string (the text value), not a list containing a single dict.

*   When _transform_stream is used in structured data generation mode (output_tool is set) and the stream contains a thinking block followed by optional text blocks and then the output tool block, the output tool block must still be recognized and its content yielded correctly, resulting in the correct structured data being returned.

*   When _transform_stream is used in structured data generation mode and the stream contains a thinking block followed by an extra text block (not the output tool) and then the output tool block, all three blocks must be processed and the output tool block's data must be the returned structured result.

*   Thinking text deltas must be concatenated with correct spacing when joined (e.g., separate thinking delta strings that form a sentence must not omit spaces between words when accumulated in thinking_content).


*   Interface details: Type: Class
Name: ContentDetails
Location: homeassistant/components/anthropic/entity.py
Description: Dataclass (with slots=True) that holds native data for AssistantContent. Stores thinking signature, redacted thinking data, and citation details. Used as the value of the `native` field in conversation.AssistantContent objects produced by the Anthropic integration.
Signature:
  Fields:
    citation_details: list[CitationDetails] = field(default_factory=list)
    thinking_signature: str | None = None
    redacted_thinking: str | None = None
  Methods:
    has_content(self) -> bool  # True if any citation_detail has length > 0
    __bool__(self) -> bool     # True if thinking_signature is not None, redacted_thinking is not None, or has_citations() is True
    has_citations(self) -> bool  # True if any citation_detail has citations
    add_citation_detail(self) -> None
    add_citation(self, citation: TextCitation) -> None
    delete_empty(self) -> None

Type: Class
Name: CitationDetails
Location: homeassistant/components/anthropic/entity.py
Description: Dataclass (with slots=True) tracking citation metadata for a content segment. Stores text position, length, and associated citation parameters.
Signature:
  Fields:
    index: int = 0
    length: int = 0
    citations: list[TextCitationParam] = field(default_factory=list)

Type: Function
Name: _convert_content
Location: homeassistant/components/anthropic/entity.py
Description: Converts a sequence of HA conversation Content objects into a list of Anthropic API MessageParam objects. When processing AssistantContent where native is a ContentDetails instance, it must emit ThinkingBlockParam when thinking_signature is set, RedactedThinkingBlockParam when redacted_thinking is set, and correct TextBlockParam entries for the content. When the resulting assistant message contains exactly one text block with no other blocks, simplifies the content to a plain string.
Signature: _convert_content(chat_content: Iterable[conversation.Content]) -> list[MessageParam]

Type: Function (async generator)
Name: _transform_stream
Location: homeassistant/components/anthropic/entity.py
Description: Transforms the Anthropic streaming API response into HA conversation delta dicts. Must store thinking block signatures in ContentDetails.thinking_signature (not as ThinkingBlock objects). Must store redacted thinking block data in ContentDetails.redacted_thinking (not as RedactedThinkingBlock objects). Must handle interleaved thinking blocks (a ThinkingBlock appearing after a tool result, i.e., when first_block is reset to True after a web search result). Must handle an output tool block appearing after a thinking block and optional extra text blocks in structured data generation mode.
Signature: _transform_stream(chat_log: conversation.ChatLog, stream: AsyncStream[MessageStreamEvent], output_tool: str | None = None) -> AsyncGenerator[conversation.AssistantContentDeltaDict | conversation.ToolResultContentDeltaDict]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.