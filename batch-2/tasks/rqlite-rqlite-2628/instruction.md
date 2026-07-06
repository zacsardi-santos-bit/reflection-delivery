I'm working on the snapshot subsystem and I'd like to introduce a new dedicated package for managing the companion checksum files that sit alongside snapshot data files.

*   The sidecar package must define a Sidecar struct with exported string fields CRC and Type, and an exported bool field Disabled.

*   The sidecar package must define a TypeCastagnoli constant whose string value is "castagnoli".

*   NewCastagnoli must accept a uint32 CRC value and return a *Sidecar with CRC set to the lowercase 8-character hex representation of that value, Type set to TypeCastagnoli, and Disabled set to false.

*   The CRC32 method on *Sidecar must parse the CRC field and return the uint32 value when Type is TypeCastagnoli and CRC is exactly 8 valid lowercase hex characters. It must return an error for any of: unknown Type, empty CRC, CRC shorter than 8 characters, CRC longer than 8 characters, or CRC containing non-hexadecimal characters.

*   WriteFile must write a JSON file to the given path containing exactly a 'crc' field (lowercase 8-character hex of the uint32 sum) and a 'type' field (value 'castagnoli'). It must return a non-nil error on any I/O failure.

*   ReadFile must read and JSON-unmarshal the sidecar file at the given path into a *Sidecar. It must return a non-nil error if the file does not exist or if the file contents are not valid JSON.

*   ReadCRC32File must read the sidecar file at the given path and return the stored CRC32 as a uint32. It must return a non-nil error if the file cannot be read or if the Type field is not a recognized algorithm type.

*   CompareFile must compute the CRC32 of the data file at dataPath, read the sidecar file at sidecarPath, and compare the two checksums. It must return (true, nil) when they match, (false, nil) when they do not match, and a non-nil error when either the data file or the sidecar file cannot be read.

*   WriteFile followed by ReadCRC32File on the same path must round-trip correctly: the uint32 written by WriteFile must equal the uint32 returned by ReadCRC32File, including the value zero.

*   The sidecar package must be located at the Go import path 'github.com/rqlite/rqlite/v10/snapshot/sidecar' and the package declaration must be 'package sidecar'.


*   Interface details: Type: Package
Name: sidecar
Location: snapshot/sidecar/sidecar.go (or equivalent file in snapshot/sidecar/ directory)
Description: New package providing structured sidecar file management for snapshot CRC integrity checking.

Type: Constant
Name: TypeCastagnoli
Location: snapshot/sidecar/
Description: String constant identifying the Castagnoli CRC32 algorithm. Its value must be "castagnoli".

Type: Struct
Name: Sidecar
Location: snapshot/sidecar/
Description: Represents a CRC sidecar record with the following exported fields:
  - CRC string        — lowercase hex-encoded checksum value
  - Type string       — algorithm type identifier (e.g., TypeCastagnoli)
  - Disabled bool     — whether integrity checking is disabled

Type: Function
Name: NewCastagnoli
Location: snapshot/sidecar/
Signature: NewCastagnoli(crc uint32) *Sidecar
Description: Constructs and returns a Sidecar with CRC set to a lowercase 8-character hex string of the given uint32, Type set to TypeCastagnoli, and Disabled set to false.

Type: Method
Name: CRC32
Location: snapshot/sidecar/
Signature: (s *Sidecar) CRC32() (uint32, error)
Description: Parses the CRC field and returns the checksum as a uint32. Returns an error if:
  - s.Type is not TypeCastagnoli (unknown type)
  - s.CRC is empty
  - s.CRC is not exactly 8 characters long (too short or too long)
  - s.CRC contains non-hexadecimal characters

Type: Function
Name: WriteFile
Location: snapshot/sidecar/
Signature: WriteFile(path string, sum uint32) error
Description: Writes a sidecar file to the given path as a JSON object with two string fields: "crc" (lowercase 8-character hex representation of sum) and "type" (set to "castagnoli"). Returns a non-nil error on failure.

Type: Function
Name: ReadFile
Location: snapshot/sidecar/
Signature: ReadFile(path string) (*Sidecar, error)
Description: Reads and JSON-parses the sidecar file at the given path. Returns a non-nil error if the file does not exist or if its contents are not valid JSON.

Type: Function
Name: ReadCRC32File
Location: snapshot/sidecar/
Signature: ReadCRC32File(path string) (uint32, error)
Description: Reads the sidecar file at the given path and returns the stored CRC32 as a uint32. Returns an error if the file cannot be read or if the type field is not a recognized algorithm (e.g., not TypeCastagnoli).

Type: Function
Name: CompareFile
Location: snapshot/sidecar/
Signature: CompareFile(dataPath, sidecarPath string) (bool, error)
Description: Computes the CRC32 of the file at dataPath, reads the sidecar file at sidecarPath, and compares the two. Returns (true, nil) if the checksums match, (false, nil) if they do not match, and a non-nil error if either file cannot be read or parsed.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.