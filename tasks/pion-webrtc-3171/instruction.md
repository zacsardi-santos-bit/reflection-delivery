Implement support for H.265 (HEVC) video streams in the pion/webrtc library by creating two new components: an H.265 bitstream reader and an H.265 RTP writer. The reader should parse raw H.265 Annex B streams into NAL units, while the writer should convert RTP packets into Annex B format.

*   Implement the `h265reader` package:
    *   Create a `NewReader(in io.Reader) (*H265Reader, error)` function that initializes an `H265Reader`.
    *   Define the `H265Reader` struct with `nalBuffer []byte` and `countOfConsecutiveZeroBytes int`.
    *   Implement the `NextNAL() (*NAL, error)` method to return the next NAL unit, handling errors for invalid streams and EOF.
    *   Implement `processByte(readByte byte) bool` to detect NAL unit boundaries.
    *   Define a `newNal(data []byte) *NAL` function for creating NAL units.
    *   Implement `parseHeader()` on `*NAL` to parse NAL headers.
    *   Define `NalUnitType` type and constants such as `NalUnitTypeVps = 32` and `NalUnitTypeSps = 33`.
    *   Include an unexported error variable `errDataIsNotH265Stream`.

*   Implement the `h265writer` package:
    *   Create a `NewWith(w io.Writer) *H265Writer` function to initialize an `H265Writer`.
    *   Implement the `WriteRTP(packet *rtp.Packet) error` method to write RTP packets in Annex B format.
    *   Implement the `Close() error` method to handle writer closure.
    *   Define `isKeyFrame(data []byte) bool` to determine if a payload is a key frame.
    *   Implement `checkAggregationPacketForKeyFrame(data []byte) bool` to check for key frames in aggregation packets.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.