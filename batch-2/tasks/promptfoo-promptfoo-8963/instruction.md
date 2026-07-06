I'm working on a Node.

*   The getLogFiles function in src/util/logs.ts must be async, returning a Promise that resolves to an array of log file descriptor objects (each with name, path, mtime, type, and size fields). It replaces the former synchronous getLogFilesSync function.

*   The export logs command must use async file system operations: it must use the promise-based access check to detect a missing log directory (treating a rejection as 'directory not found'), and must call fsPromises.readFile and fsPromises.stat on each log file during archiving.

*   When log files are successfully archived, the export logs command must log the message: 'Log files have been collected in: {output}' where {output} is the specified output file path.

*   The getCacheMappingPath function in src/providers/video/utils.ts must accept a cache key string and return the full file path: path.join(getConfigDirectoryPath(), 'media', 'video', '_cache', '{cacheKey}.json').

*   The readCacheMapping function in src/providers/video/utils.ts must be async. It must read the cache file using fsPromises.readFile with 'utf8' encoding, return the parsed JSON object on success, and return null when the file does not exist (ENOENT error code).

*   The storeCacheMapping function in src/providers/video/utils.ts must be async. It must create the cache directory with { recursive: true } before writing, then write the mapping JSON (containing at minimum a 'videoKey' field) using fsPromises.writeFile with 'utf8' encoding.

*   Video provider cache operations (OpenAI and Azure video providers) must use async file system methods (fsPromises.readFile, fsPromises.mkdir, fsPromises.writeFile) instead of synchronous equivalents for all cache read and write operations.

*   The OpenCode SDK provider must use fsPromises.rm with { recursive: true, force: true } instead of the synchronous fs.rmSync for cleaning up temporary directories after each API call.

*   The simple video strategy must use fsPromises.writeFile (async) instead of fs.writeFileSync for writing generated video content to disk.


*   Interface details: Type: Function
Name: getLogFiles
Location: src/util/logs.ts
Signature: getLogFiles(): Promise<Array<{ name: string; path: string; mtime: Date; type: string; size: number }>>
Description: Async replacement for the former synchronous getLogFilesSync. Returns a Promise that resolves to an array of log file descriptor objects, each with name, path, mtime, type, and size fields.

Type: Function
Name: getCacheMappingPath
Location: src/providers/video/utils.ts
Signature: getCacheMappingPath(cacheKey: string): string
Description: Returns the file system path for a video cache mapping JSON file. The path is constructed as: path.join(getConfigDirectoryPath(), 'media', 'video', '_cache', `${cacheKey}.json`).

Type: Function
Name: readCacheMapping
Location: src/providers/video/utils.ts
Signature: readCacheMapping(cacheKey: string): Promise<{ videoKey: string; thumbnailKey?: string; createdAt?: string } | null>
Description: Async function that reads a cache mapping file using fsPromises.readFile with 'utf8' encoding. Returns the parsed JSON object if the file exists, or null if the file does not exist (ENOENT error).

Type: Function
Name: storeCacheMapping
Location: src/providers/video/utils.ts
Signature: storeCacheMapping(cacheKey: string, videoKey: string, thumbnailKey?: string, spritesheetKey?: string, providerName?: string): Promise<void>
Description: Async function that stores a video cache mapping. First creates the cache directory using fsPromises.mkdir with { recursive: true }, then writes the mapping JSON (containing at minimum a "videoKey" field) using fsPromises.writeFile with 'utf8' encoding.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.