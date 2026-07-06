## Description

The pion/webrtc library currently supports working with H.264 encoded video, but there is no equivalent support for H.265 (HEVC) video streams. This is a gap for developers who need to handle H.265 video over WebRTC.

Two new components are needed:

1. **An H.265 bitstream reader** — a component that accepts a raw H.265 Annex B stream and parses it into individual network access layer (NAL) units, exposing each unit's type, header flags, layer ID, and temporal identifier. It must handle error conditions gracefully, including streams that are not valid H.265 data (returning an appropriate error) and streams that are fully consumed (returning end-of-file).

2. **An H.265 RTP writer** — a component that accepts H.265 RTP packets and writes them in Annex B format (with the standard 4-byte start code) to an underlying output destination. The writer must also be able to identify whether a given packet represents a key frame, including when that packet is a fragmentation unit or an aggregation packet containing multiple NAL units.

## Expected Behavior

- Reading an invalid (non-H.265) bitstream should return a descriptive error.
- Reading a valid stream should return successive NAL units with correct type and header fields parsed.
- Writing an RTP packet should prepend the 4-byte Annex B start code to the output.
- Key frame detection should work for direct NAL units as well as fragmentation and aggregation packet types.
- Empty RTP payloads should be silently ignored.

## Why This Matters

Without H.265 support, developers building WebRTC applications with HEVC video have no idiomatic way to save or process the media stream within the pion ecosystem, forcing them to implement their own parsing and writing logic outside the library.
