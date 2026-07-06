## Description

Container image manifest handling currently has no support for encryption operations when updating layer information. The OCI image format supports encrypted layers through a specific media type convention (appending an encrypted marker to the layer media type), but the code that updates manifest layer information doesn't implement this, so encryption operations are silently ignored or produce incorrect results.

Additionally, Docker-style manifest formats (both the older v1 format and the current v2 schema2 format) cannot represent encrypted image layers, but there are currently no guards that reject encryption or decryption attempts on these formats. This means a caller can accidentally request encryption on an incompatible format without getting an error.

Finally, when converting an encrypted OCI image to a Docker-compatible format, the current code has no special handling: it either crashes or silently produces a broken manifest. The correct behavior is to require simultaneous decryption during such a conversion — an encrypted image cannot simply be "converted" to a Docker format without first being decrypted.

## Expected Behavior

- Updating OCI manifest layer infos with an encryption operation should correctly set the layer media type to reflect encryption, and updating with a decryption operation should strip the encrypted marker from the media type.
- Updating Docker schema1 or schema2 manifest layer infos with any encryption or decryption operation should return an error, since these formats cannot represent encrypted layers.
- When converting a Docker-format image to OCI format, requesting encryption on layers should succeed and produce the correct encrypted media types.
- When converting a Docker-format image to another Docker format with encryption requested, the conversion should fail with an error.
- Converting an encrypted OCI image to a Docker format without simultaneously decrypting should fail.
- Converting an encrypted OCI image to a Docker format while simultaneously decrypting the layers should succeed.
- The source image object must not be modified as a side effect of a conversion operation.

## Why This Matters

Without these fixes, working with encrypted container images is unreliable: encryption can be silently applied to formats that don't support it, encrypted images cannot be correctly converted to other formats, and the tooling gives no clear feedback when an unsupported operation is requested.
