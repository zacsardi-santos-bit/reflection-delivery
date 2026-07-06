I've been looking at the always-available streaming configuration and found a few things that need fixing.

*   When a path is loaded from configuration without an explicit 'alwaysAvailableTracks' value, the resulting Path struct's AlwaysAvailableTracks field must be an empty slice ([]AlwaysAvailableTrack{}) — the system must not add any default tracks (such as H264) automatically.

*   When both 'alwaysAvailableFile' (non-empty string) and 'alwaysAvailableTracks' (containing at least one entry) are set for the same path, the Load function must return an error with the exact message: "'alwaysAvailableFile' and 'alwaysAvailableTracks' cannot be used together".

*   When 'alwaysAvailableFile' references a file whose bytes at offset 4–7 are not the MP4 signature 'ftyp', the Load function must return an error formatted as: "invalid 'alwaysAvailableFile': file is not MP4, magic bytes are [X Y Z W]" where X Y Z W are decimal byte values — the bracket notation must NOT be surrounded by single quotes.


*   Interface details: Type: Function
Name: Load
Location: internal/conf/conf.go (or equivalent entry point in internal/conf/)
Signature: Load(confPath string, additionalParams interface{}, additionalParams2 interface{}) (conf, closeFunc, error)
Description: Loads and validates a MediaMTX configuration file. Returns an error if validation fails. The tests call this as Load(tmpConf, nil, nil). This function already exists and must be modified to reflect updated Path validation behavior.

Type: Struct
Name: Path
Location: internal/conf/path.go
Description: Represents per-path configuration. The AlwaysAvailableTracks field ([]AlwaysAvailableTrack) must default to an empty slice — the setDefaults() method must NOT initialize it with a default H264 track. The validate() method must reject configurations where both AlwaysAvailableFile (non-empty) and AlwaysAvailableTracks (non-empty) are set, returning an error with exact text: "'alwaysAvailableFile' and 'alwaysAvailableTracks' cannot be used together".

Type: Function
Name: checkMP4MagicBytes
Location: internal/conf/path.go
Signature: checkMP4MagicBytes(f io.ReadSeeker) error
Description: Internal helper that validates an MP4 file by checking bytes 4–7 for the "ftyp" signature. When the check fails, must return an error formatted as: "file is not MP4, magic bytes are %v" — using Go's default slice formatting without any surrounding single quotes. This is wrapped by the caller into "invalid 'alwaysAvailableFile': <error>".

Type: Struct
Name: AlwaysAvailableTrack
Location: internal/conf/path.go
Description: Represents a single track entry in the AlwaysAvailableTracks list. Has at minimum a Codec string field. Used in Path.AlwaysAvailableTracks slice.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.