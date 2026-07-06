I'm embedding Excalidraw in my application and I need to be able to control the image size limits it enforces.

*   The resizeImageFile function must return the original File object (by reference, not a copy) when the image's largest dimension is already within the specified maxWidthOrHeight limit.

*   The ExcalidrawProps interface must include an optional imageOptions property of type ImageOptions, where ImageOptions is defined as a partial object with optional maxWidthOrHeight (number) and maxFileSizeBytes (number) fields. Both types must be exported from packages/excalidraw/types.ts.

*   When the Excalidraw component is given imageOptions with a maxWidthOrHeight value and an image is dropped, it must pass that maxWidthOrHeight value to resizeImageFile when processing the image.

*   When the Excalidraw component is given imageOptions with a maxFileSizeBytes value and a dropped image file exceeds that size, the application error message must be set to the exact string: 'File is too big. Maximum allowed size is {N}MB.' where N is Math.trunc(maxFileSizeBytes / 1024 / 1024).


*   Interface details: Type: Function
Name: resizeImageFile
Location: packages/excalidraw/data/blob.ts
Signature: resizeImageFile(file: File, opts: { maxWidthOrHeight: number; outputType?: string }) -> Promise<File>
Description: Resizes an image file to fit within the given maxWidthOrHeight. If the image dimensions are already within the limit, returns the original File object unchanged (same reference). If outputType is not provided or matches file.type, dimensions are checked before initiating any resize.

Type: Type alias
Name: ImageOptions
Location: packages/excalidraw/types.ts
Signature: type ImageOptions = Partial<{ maxWidthOrHeight: number; maxFileSizeBytes: number }>
Description: Configuration object for image insertion constraints. Both fields are optional.

Type: Interface property
Name: imageOptions
Location: packages/excalidraw/types.ts
Signature: imageOptions?: ImageOptions  (on ExcalidrawProps interface)
Description: Optional prop added to ExcalidrawProps that lets host applications configure image dimension and file size limits. When provided, maxWidthOrHeight is forwarded to resizeImageFile and maxFileSizeBytes is checked after resizing; if the file exceeds the limit an error is thrown.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.