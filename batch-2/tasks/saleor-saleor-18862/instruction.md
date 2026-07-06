I'm working on a background task that fetches images for product media records that have an external URL but no locally stored image.

*   The `is_image_mimetype` function must be importable from `saleor/core/utils/validators.py`. It accepts a MIME type string and returns True if the type starts with 'image/', otherwise False.

*   The `is_valid_image_content_type` function must be importable from `saleor/core/utils/validators.py`. It accepts an optional content type string and returns True only for these raster image types: image/jpeg, image/jpg, image/png, image/gif, image/bmp, image/tiff, image/webp, image/avif. It must return False for image/svg+xml, any non-image content type, and None.

*   The `get_mime_type` function must be importable from `saleor/core/utils/validators.py`. It accepts an optional Content-Type header string and returns only the base MIME type (without parameters like charset). It must return None when given None, return empty string when given empty string, strip leading/trailing whitespace, and normalize the result to lowercase.

*   The `fetch_product_media_image_task` Celery task must be importable from `saleor/product/tasks.py`. It accepts a single integer argument representing a ProductMedia primary key.

*   If the ProductMedia already has an image set, `fetch_product_media_image_task` must leave the image unchanged.

*   If no ProductMedia exists with the given primary key, `fetch_product_media_image_task` must complete without error.

*   If the ProductMedia has no external_url and no image, `fetch_product_media_image_task` must complete without deleting the record.

*   If the ProductMedia has a type other than IMAGE (e.g., VIDEO), `fetch_product_media_image_task` must complete without setting an image on the record.

*   When the HTTP response for the image URL returns a non-image content type (e.g., text/plain) or an unsupported image type (e.g., image/svg+xml), `fetch_product_media_image_task` must delete the ProductMedia record.

*   When the HTTP response returns a valid supported image content type and valid image bytes, `fetch_product_media_image_task` must save the image to the ProductMedia record and set external_url to None.

*   When the HTTP request raises a transient network exception (e.g., RequestException or InvalidSchema), `fetch_product_media_image_task` must re-raise the exception (allowing retry), and the ProductMedia record must survive a single failed attempt. However, when all retries are exhausted and the on_failure hook executes, the ProductMedia record must be deleted.

*   When the HTTP response has a 5xx server error status code (500, 502, 503), `fetch_product_media_image_task` must raise an HTTPError to trigger a retry, and the ProductMedia must remain with its external_url intact.

*   When the HTTP response has a non-5xx error status code (including informational, redirect, client error codes such as 100, 199, 300, 301, 401, 404, 499), `fetch_product_media_image_task` must delete the ProductMedia record (no retry).

*   When the downloaded content has an invalid image structure (corrupt bytes or invalid EXIF data causing a SyntaxError), `fetch_product_media_image_task` must delete the ProductMedia record.

*   The task uses an `HTTPClient` class available at `saleor.product.tasks.HTTPClient` with a `send_request` class method that operates as a context manager. Image processing logic resides in `saleor/product/utils/tasks_utils.py` and uses `PIL.Image.open`.


*   Interface details: Type: Function
Name: is_image_mimetype
Location: saleor/core/utils/validators.py
Signature: is_image_mimetype(mimetype: str) -> bool
Description: Returns True if the given MIME type string starts with "image/", False otherwise.

Type: Function
Name: is_valid_image_content_type
Location: saleor/core/utils/validators.py
Signature: is_valid_image_content_type(content_type: Optional[str]) -> bool
Description: Returns True only for supported raster image content types: image/jpeg, image/jpg, image/png, image/gif, image/bmp, image/tiff, image/webp, image/avif. Returns False for image/svg+xml, any other content type, and None.

Type: Function
Name: get_mime_type
Location: saleor/core/utils/validators.py
Signature: get_mime_type(content_type_header: Optional[str]) -> Optional[str]
Description: Parses and returns only the base MIME type from a Content-Type header string (strips parameters such as charset). Returns None for None input, empty string for empty string input. Result is lowercased and whitespace-stripped.

Type: Celery Task
Name: fetch_product_media_image_task
Location: saleor/product/tasks.py
Signature: fetch_product_media_image_task(product_media_pk: int)
Description: Celery task that fetches and stores an image from a ProductMedia record's external_url. Skips records that already have an image, don't exist, have no external_url, or are not of IMAGE type. Uses HTTPClient (available at saleor.product.tasks.HTTPClient) with a send_request class method acting as a context manager to download the image. Image processing (including EXIF parsing via PIL) is handled in saleor/product/utils/tasks_utils.py. On success, saves the image and sets external_url to None. Deletes the ProductMedia record on invalid content type, unsupported image format (including SVG), non-5xx HTTP errors, invalid image data, or after on_failure hook fires. Raises HTTPError for 5xx responses to trigger retry. Raises RequestException or InvalidSchema to allow Celery retry; on_failure hook deletes the record when retries are exhausted.

Type: Module
Name: tasks_utils
Location: saleor/product/utils/tasks_utils.py
Description: Utility module used by fetch_product_media_image_task for image processing. Contains logic that calls PIL's Image.open (patchable at saleor.product.utils.tasks_utils.Image.open) to validate and process downloaded image content.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.