# Fetch product media images from external URLs via background task

## Description

When product media records are created with an external URL pointing to an image, there is currently no automated background task to download the image and attach it to the product media record. This means images referenced by external URL remain undownloaded, and the system has no way to process them into locally stored assets.

We need a background task that:
- Fetches images from the external URL stored on a product media record
- Validates that the downloaded content is actually a supported image type (raster formats only — SVG and non-image content should be rejected)
- Saves valid images to the product media record and clears the external URL
- Gracefully handles transient failures by retrying, and cleans up the product media record after permanent failures
- Skips records that already have an image, are not of image type, or have neither image nor external URL

## Expected Behavior

- If a product media record already has an image, the task does nothing
- If a product media record doesn't exist, the task completes silently
- If a product media record has no external URL and no image, the task completes without deleting it
- Records with a non-image media type (e.g. video) are skipped
- Non-image or unsupported image content types (including SVG) cause the record to be deleted
- Valid images are downloaded, stored, and the external URL is cleared
- Transient server errors (5xx) trigger automatic retries; the record survives retry attempts
- Permanent failures (non-5xx unexpected responses, network errors after retries exhausted, corrupt image data) result in deletion of the product media record

## Additional Refactoring

Three MIME type utility functions (checking whether a MIME type is an image type, checking whether a content type is a supported image format, and parsing a base MIME type from a Content-Type header string) should be moved from the file-upload validation module to a more general utility module so they can be reused across the codebase — including by the new task. The moved functions must continue to behave identically, except that the supported image content type check must now explicitly treat SVG as an unsupported format.

## Why This Matters

Without this task, product media records that reference external image URLs cannot be automatically processed into locally stored assets. The task enables the full lifecycle of external image ingestion with proper error handling and cleanup.
