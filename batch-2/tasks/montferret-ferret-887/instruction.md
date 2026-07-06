I'm working on a code formatter for a query language and running into a bug with how it handles the configured maximum line width.

*   The printer in pkg/formatter/internal must expose a lineColumn integer field that tracks the current byte-width column position. After writing any content, lineColumn must equal the byte length of the content written since the last newline.

*   The printer's writeRaw method must process its input by splitting on newline bytes. For each non-empty segment before a newline, it must write the segment to the output, set atLineStart to false, update lastWasSpace based on whether the last byte of the segment is a space, and add the byte length of that segment to lineColumn.

*   When writeRaw encounters a newline, it must set sawHardNewline to true. In normal mode, it must write a literal newline, then set atLineStart=true, lastWasSpace=false, and reset lineColumn to 0. In forceSingleLine mode it must write a space character instead of the newline (incrementing lineColumn by 1), while still setting sawHardNewline=true.

*   After writeRaw processes content that follows the final newline in the input, atLineStart must be false and lineColumn must equal the byte length of that trailing content.

*   When the formatter decides whether to render an expression inline, it must compare (current column position + inline string byte length) against the configured print width, not just the inline string length alone. When the printer is at a line start, the projected indentation width must also be included in that comparison.

*   Arrays whose elements include object literals or nested array/object structured elements must always be formatted as multiline (one element per indented line), regardless of whether the inline form would fit within the print width.

*   Object literals that contain more than four property assignments must always be formatted as multiline.

*   A comment that appears between an opening bracket or brace and the first element must cause the enclosing array or object to be formatted as multiline, with the comment preserved on its own indented line before the first element.

*   Formatted output must always end with exactly two newline characters (\n\n).

*   Multiline arrays and objects must indent their contents using four spaces per indentation level; nested structures must apply indentation recursively.


*   Interface details: Type: Struct Field
Name: lineColumn
Location: pkg/formatter/internal/printer.go
Description: Integer field on the printer struct that tracks the current column position as a byte count (not a rune count). It is incremented by the byte length of each segment written to the output, reset to 0 after every hard newline, and incremented by 1 when a newline is collapsed to a space in force-single-line mode. The field must be accessible within the internal package (unexported).

Type: Method (updated behavior)
Name: writeRaw
Location: pkg/formatter/internal/printer.go
Signature: writeRaw(s string)
Description: Writes a raw string to the printer output, processing it in segments split on the newline byte. For each segment before a newline: writes the segment, sets atLineStart=false, sets lastWasSpace based on whether the last byte is a space, and increments lineColumn by len(segment). When a newline is found, sets sawHardNewline=true; in normal mode writes a literal newline then sets atLineStart=true, lastWasSpace=false, lineColumn=0; in forceSingleLine mode calls space() instead (which increments lineColumn by 1) and still sets sawHardNewline=true. After processing trailing content following the last newline, atLineStart must be false and lineColumn must equal the byte length of that trailing content.

Type: Function (existing, must exist)
Name: newPrinter
Location: pkg/formatter/internal/printer.go
Signature: newPrinter(out io.Writer, opts *Options) *printer
Description: Constructor for the printer struct. Already exists in the codebase; must remain compatible with this signature.

Type: Function (existing, must exist)
Name: DefaultOptions
Location: pkg/formatter/internal/options.go (or similar)
Signature: DefaultOptions() *Options
Description: Returns a default Options value. Already exists in the codebase; must remain compatible with this signature.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.