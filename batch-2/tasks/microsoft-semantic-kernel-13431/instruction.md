I'm working on Semantic Kernel and I need to add proper support for when a kernel function returns an image as its result.

*   FunctionCallsProcessor.ProcessFunctionResult must return an ImageContent object as-is (without JSON serialization) when the function result is of type ImageContent, preserving its binary data and MimeType unchanged.

*   FunctionCallsProcessor must expose a public constant string named ImageContentNotSupportedErrorMessage with the value "Error: This model does not support image content in tool results."

*   AssistantMessageFactory.GetMessageContents must return a single MessageContent item whose Text property equals "Error: This model does not support image content in tool results." when the input ChatMessageContent contains a FunctionResultContent whose result is an ImageContent.

*   ResponseThreadActions must expose a public static method GetFunctionResultAsString(object?) that: returns "Error: This model does not support image content in tool results." when passed an ImageContent; returns the string verbatim when passed a string; returns string.Empty when passed null.

*   GeminiRequest.FromChatHistoryAndExecutionSettings must encode a tool result ImageContent that has binary data by placing the base64-encoded bytes and MimeType in FunctionResponse.Parts[0].InlineData (InlineData.InlineData = Convert.ToBase64String(bytes), InlineData.MimeType = the image MimeType).

*   GeminiRequest.FromChatHistoryAndExecutionSettings must throw InvalidOperationException with message "ImageContent in function result must contain binary data." when the tool result is an ImageContent that has only a URI and no binary data.

*   GeminiRequest.FromChatHistoryAndExecutionSettings must throw InvalidOperationException with message "Image content MimeType is empty." when the tool result is an ImageContent that has binary data but a null or empty MimeType.


*   Interface details: Type: Class
Name: FunctionCallsProcessor
Location: dotnet/src/InternalUtilities/connectors/AI/FunctionCalling/FunctionCallsProcessor.cs
Description: Processes function call results for AI backends. Return type of ProcessFunctionResult must be changed from string to object to support pass-through of ImageContent.
Signature: static object ProcessFunctionResult(object functionResult) -> object
Notes: When functionResult is of type ImageContent, must return it as-is without serialization. For all other types, behavior remains unchanged. Must also expose a public constant: public const string ImageContentNotSupportedErrorMessage = "Error: This model does not support image content in tool results."

Type: Class
Name: ResponseThreadActions
Location: dotnet/src/Agents/OpenAI/Internal/ResponseThreadActions.cs
Description: Handles actions for response thread processing in the OpenAI Responses API agent. Must expose a new static method for converting function results to strings.
Signature: internal static string GetFunctionResultAsString(object? result) -> string
Notes: Returns "Error: This model does not support image content in tool results." when passed an ImageContent instance; returns the string verbatim when passed a string; returns string.Empty when passed null.

Type: Class
Name: AssistantMessageFactory
Location: dotnet/src/Agents/OpenAI/Internal/AssistantMessageFactory.cs
Description: Factory for creating OpenAI Assistants API messages from Semantic Kernel chat messages.
Signature: static IEnumerable<MessageContent> GetMessageContents(ChatMessageContent message) -> IEnumerable<MessageContent>
Notes: When the message contains a FunctionResultContent whose result is an ImageContent, must return a single MessageContent item whose Text property equals "Error: This model does not support image content in tool results."

Type: Class
Name: GeminiRequest
Location: dotnet/src/Connectors/Connectors.Google/Core/Gemini/Models/GeminiRequest.cs
Description: Represents a request to the Gemini API, constructed from chat history and execution settings.
Signature: static GeminiRequest FromChatHistoryAndExecutionSettings(ChatHistory chatHistory, GeminiPromptExecutionSettings executionSettings) -> GeminiRequest
Notes: When a tool result FunctionResult contains an ImageContent with binary data and a MimeType, the corresponding FunctionResponse must have a Parts array where Parts[0].InlineData.MimeType equals the image MIME type and Parts[0].InlineData.InlineData equals the base64-encoded image bytes (Convert.ToBase64String). Must throw InvalidOperationException with message "ImageContent in function result must contain binary data." when the ImageContent has no binary data (URI-only). Must throw InvalidOperationException with message "Image content MimeType is empty." when the ImageContent has binary data but a null or empty MimeType.

Type: Class
Name: GeminiPart.FunctionResponsePart (modification)
Location: dotnet/src/Connectors/Connectors.Google/Core/Gemini/Models/GeminiPart.cs
Description: The existing FunctionResponsePart nested class inside GeminiPart must be extended with a new Parts property to support multimodal tool responses.
Signature: FunctionResponsePartContent[]? Parts { get; set; }  — serialized as JSON property "parts", ignored when null
Notes: This property holds the multimodal content parts of the function response.

Type: Class
Name: GeminiPart.FunctionResponsePart.FunctionResponsePartContent (new nested class)
Location: dotnet/src/Connectors/Connectors.Google/Core/Gemini/Models/GeminiPart.cs
Description: A new nested class inside FunctionResponsePart to represent individual content parts within a multimodal function response.
Signature: InlineDataPart? InlineData { get; set; }  — serialized as JSON property "inlineData", ignored when null
Notes: The InlineDataPart type already exists in the codebase with MimeType (string) and InlineData (string, the base64-encoded bytes) properties. This new class wraps it for use inside FunctionResponse.Parts.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.