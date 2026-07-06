I need to add a new gRPC-only service to Qdrant that lets authorized clients read raw files from a collection's on-disk storage.

*   The StorageReadService must be constructible via StorageReadService::new(dispatcher: Arc<Dispatcher>) and must reside in the src/tonic/api/storage_read_api module.

*   The module must export a STREAM_CHUNK_SIZE constant (u64) that controls the maximum chunk size when streaming file data; reads larger than STREAM_CHUNK_SIZE bytes must be split into a first chunk of exactly STREAM_CHUNK_SIZE bytes followed by a final chunk containing the remainder.

*   The file_exists method must return FileExistsResponse with exists=true when the file is present and exists=false when it is absent.

*   The list_files method must return ListFilesResponse with paths that are relative to the collection directory and contain only entries whose names start with the given prefix_path; files not matching the prefix must be excluded.

*   The file_length method must return FileLengthResponse with the byte count of the requested file; it must return gRPC status Code::NotFound when the file does not exist or when the collection does not exist.

*   The read_bytes method must return ReadBytesResponse with the requested byte slice (starting at byte_offset, with the specified length); it must return Code::OutOfRange when the requested range extends beyond the end of the file.

*   The read_bytes_stream method must return a streaming sequence of ReadBytesStreamResponse messages, each carrying a data chunk; for a length of zero on an existing file it must return an empty stream; for a length of zero on a non-existent file it must return Code::NotFound; for a length that exceeds the file size it must return Code::OutOfRange.

*   The read_whole method must return ReadWholeResponse with the complete contents of the requested file.

*   The read_batch method must return ReadBatchResponse whose data field is a list of byte slices, one per requested range, in the same order as the ranges list.

*   The read_multi method must return ReadMultiResponse whose data field is a list of byte slices, one per entry in the reads list, in request order; it must return Code::InvalidArgument for any entry whose path field is empty.

*   All path arguments must be validated before filesystem access: paths containing '..' components must be rejected with Code::InvalidArgument and an error message that contains the text 'Invalid path component'; paths equal to '.' or starting with './' must also be rejected with Code::InvalidArgument.

*   On Unix, if the resolved collection directory is a symlink that points outside the expected storage root, or if a file path within the collection directory resolves through a symlink to a location outside that directory (including to a sibling collection's directory), the request must be rejected with Code::PermissionDenied.

*   All methods must enforce collection-level access control: if the authenticated caller's access token is scoped to a different collection than the one named in the request, the request must be rejected with Code::PermissionDenied.

*   When the requested collection does not exist on the server, methods that perform file lookups must return Code::NotFound.


*   Interface details: Type: Module
Name: storage_read_api
Location: src/tonic/api/storage_read_api/
Description: Module containing the StorageReadService implementation and the tests submodule. The tests file is at src/tonic/api/storage_read_api/tests.rs and uses `use super::*;` to import everything from this module.

Type: Constant
Name: STREAM_CHUNK_SIZE
Location: src/tonic/api/storage_read_api/mod.rs (or lib.rs in the module)
Signature: const STREAM_CHUNK_SIZE: u64 = <value>;
Description: Maximum number of bytes in a single streaming chunk for read_bytes_stream responses. Reads larger than this value are split: the first chunk carries exactly STREAM_CHUNK_SIZE bytes and the final chunk carries the remainder. Must be a u64 constant visible within the module (accessible via `use super::*` from the tests submodule).

Type: Type
Name: MmapFile
Location: src/tonic/api/storage_read_api/mod.rs (or imported and re-exported)
Description: Concrete file type used as the type parameter for StorageReadService. Tests instantiate StorageReadService<MmapFile> and the type must be accessible via `use super::*`.

Type: Struct
Name: StorageReadService
Location: src/tonic/api/storage_read_api/mod.rs
Description: gRPC service implementation for reading collection storage files. Parameterized over a file type F. The concrete type used in tests is StorageReadService<MmapFile>.

Type: Function
Name: StorageReadService::new
Location: src/tonic/api/storage_read_api/mod.rs
Signature: fn new(dispatcher: Arc<Dispatcher>) -> StorageReadService<MmapFile>
Description: Constructs a StorageReadService from the given dispatcher. The Dispatcher type comes from storage::content_manager::toc::Dispatcher.

Type: Method
Name: StorageReadService::file_exists
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn file_exists(&self, request: Request<FileExistsRequest>) -> Result<Response<FileExistsResponse>, Status>
Description: Checks whether a file exists at the given path within a collection's storage directory. Returns FileExistsResponse { exists: true } if the file exists, { exists: false } if it does not. Rejects path traversal (.. components) with Code::InvalidArgument and error message containing "Invalid path component". Rejects paths equal to "." or starting with "./" with Code::InvalidArgument. On Unix, rejects symlink escapes with Code::PermissionDenied. Rejects access to a collection not covered by the caller's auth token with Code::PermissionDenied.

Type: Method
Name: StorageReadService::list_files
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn list_files(&self, request: Request<ListFilesRequest>) -> Result<Response<ListFilesResponse>, Status>
Description: Lists files within a collection's storage directory whose relative paths start with prefix_path. Returns ListFilesResponse { paths: Vec<String> } where paths are relative to the collection directory.

Type: Method
Name: StorageReadService::file_length
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn file_length(&self, request: Request<FileLengthRequest>) -> Result<Response<FileLengthResponse>, Status>
Description: Returns FileLengthResponse { length: u64 } with the byte size of the specified file. Returns Code::NotFound for missing files and for missing collections.

Type: Method
Name: StorageReadService::read_bytes
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn read_bytes(&self, request: Request<ReadBytesRequest>) -> Result<Response<ReadBytesResponse>, Status>
Description: Reads a byte range (byte_offset, length) from a file and returns ReadBytesResponse { data: Vec<u8> }. Returns Code::OutOfRange when the requested range exceeds the file size.

Type: Method
Name: StorageReadService::read_bytes_stream
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn read_bytes_stream(&self, request: Request<ReadBytesStreamRequest>) -> Result<Response<impl Stream<Item = Result<ReadBytesStreamResponse, Status>>>, Status>
Description: Streams a byte range as a sequence of ReadBytesStreamResponse chunks. Reads larger than STREAM_CHUNK_SIZE are split into chunks: first chunk is exactly STREAM_CHUNK_SIZE bytes, subsequent chunks carry the remainder. Returns an empty stream for length=0 on an existing file. Returns Code::NotFound for length=0 on a non-existent file. Returns Code::OutOfRange when the requested length exceeds the file size.

Type: Method
Name: StorageReadService::read_whole
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn read_whole(&self, request: Request<ReadWholeRequest>) -> Result<Response<ReadWholeResponse>, Status>
Description: Reads the entire contents of a file and returns ReadWholeResponse { data: Vec<u8> }.

Type: Method
Name: StorageReadService::read_batch
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn read_batch(&self, request: Request<ReadBatchRequest>) -> Result<Response<ReadBatchResponse>, Status>
Description: Reads multiple byte ranges (each a ReadBatchRange with byte_offset and length) from a single file. Returns ReadBatchResponse { data: Vec<Vec<u8>> } with one entry per range in request order.

Type: Method
Name: StorageReadService::read_multi
Location: src/tonic/api/storage_read_api/mod.rs
Signature: async fn read_multi(&self, request: Request<ReadMultiRequest>) -> Result<Response<ReadMultiResponse>, Status>
Description: Reads byte ranges from multiple files. Each entry in reads is a ReadMultiEntry { path, byte_offset, length } from a potentially different file. Returns ReadMultiResponse { data: Vec<Vec<u8>> } with one entry per read in request order. Returns Code::InvalidArgument for any entry with an empty path.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.