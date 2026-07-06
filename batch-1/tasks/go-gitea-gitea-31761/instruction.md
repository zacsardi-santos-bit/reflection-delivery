Implement a new internal zstd compression module to provide a shared, consistent API for compression across the project. This module should support both standard streaming and seekable compression/decompression functionalities. Ensure compatibility and efficient random access for seekable streams.

*   Create a new Go package at `modules/zstd` with import path `code.gitea.io/gitea/modules/zstd`.
*   Implement `NewWriter`:
    *   Accepts an `io.Writer` and optional `WriterOptions`.
    *   Returns a `(*Writer, error)`.
    *   `Writer` must implement `io.WriteCloser` and compress data correctly.
    *   Ensure `Writer` flushes and closes properly when `Close()` is called.
*   Implement `NewReader`:
    *   Accepts an `io.Reader` and optional `ReaderOptions`.
    *   Returns a `(*Reader, error)`.
    *   `Reader` must implement `io.ReadCloser` and decompress data correctly.
    *   Ensure `Reader` closes cleanly.
*   Implement `WithEncoderLevel`:
    *   Accepts an `EncoderLevel` value.
    *   Returns a `WriterOption` to configure the encoder's compression level.
*   Implement `WithDecoderLowmem`:
    *   Accepts a `bool`.
    *   Returns a `ReaderOption` to configure low-memory decoding mode.
*   Define `SpeedBestCompression`:
    *   Exported constant of type `EncoderLevel` representing the highest compression level.
*   Implement `NewSeekableWriter`:
    *   Accepts an `io.Writer` and a `blockSize` int.
    *   Returns a `(*SeekableWriter, error)`.
    *   Writes data as indexed seekable frames with approximately `blockSize` uncompressed bytes.
*   Implement `NewSeekableReader`:
    *   Accepts an `io.ReadSeeker`.
    *   Returns a `(*SeekableReader, error)`.
    *   Returns an error if the data lacks a seekable index.
*   Implement `SeekableReader.Seek`:
    *   Accepts an `offset` and `whence`.
    *   Seeks to the correct position in the uncompressed stream.
    *   Returns the resulting absolute offset.
    *   Ensure seeking involves at most 3 underlying `Seek` calls and reads fewer bytes than `2 * blockSize`.
*   Implement `SeekableReader.Close`:
    *   Closes the reader and the underlying reader if it implements `io.Closer`.
*   Ensure data written with `NewSeekableWriter` is readable by both `NewSeekableReader` and `NewReader`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.