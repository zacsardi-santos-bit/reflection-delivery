I'm working on the Go client library for this project and I need to add a shared binary encoding and decoding package.

*   The Reader type must be constructable via NewReader(payload []byte) *Reader in the foreign/go/internal/codec package.

*   Reader must read values sequentially using little-endian byte order: U8() reads 1 byte as uint8, U16() reads 2 bytes as uint16, U32() reads 4 bytes as uint32, U64() reads 8 bytes as uint64, F32() reads 4 bytes as float32 (IEEE 754).

*   Reader.Str(n int) must read exactly n bytes and return them as a string without any length prefix.

*   Reader.U32LenStr() must read a 4-byte little-endian uint32 length prefix followed by that many bytes as a string.

*   Reader.U8LenStr() must read a 1-byte uint8 length prefix followed by that many bytes as a string.

*   Reader.Raw(n int) must read exactly n bytes and return them as a []byte.

*   Reader.Obj(n int, v encoding.BinaryUnmarshaler) must read n bytes and call v.UnmarshalBinary with them; if UnmarshalBinary returns an error, that error must be stored in the Reader.

*   Reader.Remaining() must return the number of bytes in the buffer that have not yet been consumed.

*   Reader.Err() must return the accumulated error, or nil if none occurred.

*   When a read would exceed the available buffer (overrun), the Reader must set an error whose message starts with the prefix 'reader: need ' and includes the caller's file and line number in 'file:line' format within the error string.

*   Once Reader.Err() is non-nil, all subsequent read methods (U8, U16, U32, U64, F32, Str, Raw, U32LenStr, U8LenStr, Obj) must be no-ops returning zero values, and the error must not be overwritten.

*   The Writer type must be constructable via NewWriter() *Writer and NewWriterCap(n int) *Writer in the foreign/go/internal/codec package. NewWriterCap must pre-allocate a buffer of at least n bytes so that writes totaling n bytes do not cause reallocation.

*   Writer must have an exported or unexported field named 'p' of type []byte that holds the accumulated buffer.

*   Writer must encode and append values in little-endian byte order: U8(uint8) appends 1 byte, U16(uint16) appends 2 bytes, U32(uint32) appends 4 bytes, U64(uint64) appends 8 bytes, F32(float32) appends 4 bytes (IEEE 754).

*   Writer.Str(s string) must append the raw bytes of the string with no length prefix.

*   Writer.U32LenStr(s string) must append a 4-byte little-endian uint32 length prefix followed by the string bytes.

*   Writer.U8LenStr(s string) must append a 1-byte uint8 length prefix followed by the string bytes. If the string length exceeds 255 bytes, it must set an error whose message matches the pattern '^string length (\d+) exceeds 255' and includes the caller's file and line number.

*   Writer.Raw(b []byte) must append all bytes from the given slice.

*   Writer.Obj(v encoding.BinaryMarshaler) must call v.MarshalBinary(), append the returned bytes, and if MarshalBinary returns an error, store it in the Writer with the caller's file and line number included in the error message.

*   Writer.Bytes() must return the accumulated buffer. When Writer.Err() is non-nil, Bytes() must return a zero-length (empty) byte slice.

*   Writer.Err() must return the accumulated error, or nil if none occurred.

*   Once Writer.Err() is non-nil, all subsequent write methods (U8, U16, U32, U64, F32, Str, U32LenStr, U8LenStr, Raw, Obj) must be no-ops, the buffer must remain unchanged, and the existing error must not be overwritten.


*   Interface details: Type: Struct
Name: Reader
Location: foreign/go/internal/codec/reader.go
Description: A binary deserializer that reads sequentially from a byte slice using little-endian encoding. Accumulates errors silently; once an error is set, all subsequent reads return zero values without overwriting the error.
Signature:
  NewReader(payload []byte) *Reader
  (r *Reader) U8() uint8
  (r *Reader) U16() uint16
  (r *Reader) U32() uint32
  (r *Reader) U64() uint64
  (r *Reader) F32() float32
  (r *Reader) Str(n int) string
  (r *Reader) U32LenStr() string
  (r *Reader) U8LenStr() string
  (r *Reader) Raw(n int) []byte
  (r *Reader) Obj(n int, v encoding.BinaryUnmarshaler)
  (r *Reader) Remaining() int
  (r *Reader) Err() error

Type: Struct
Name: Writer
Location: foreign/go/internal/codec/writer.go
Description: A binary serializer that appends sequentially to an internal byte slice using little-endian encoding. Has an internal field named `p` of type `[]byte` for the buffer. Accumulates errors silently; once an error is set, all subsequent writes are no-ops and Bytes() returns an empty slice.
Signature:
  NewWriter() *Writer
  NewWriterCap(n int) *Writer
  (w *Writer) U8(v uint8)
  (w *Writer) U16(v uint16)
  (w *Writer) U32(v uint32)
  (w *Writer) U64(v uint64)
  (w *Writer) F32(v float32)
  (w *Writer) Str(s string)
  (w *Writer) U32LenStr(s string)
  (w *Writer) U8LenStr(s string)
  (w *Writer) Raw(b []byte)
  (w *Writer) Obj(v encoding.BinaryMarshaler)
  (w *Writer) Bytes() []byte
  (w *Writer) Err() error


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.