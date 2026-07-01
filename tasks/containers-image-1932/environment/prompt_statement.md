I'm working on adding encryption support to container image manifest handling in the containers/image library. Right now, when I try to mark image layers as encrypted while updating an OCI manifest's layer information, nothing happens correctly — the media type doesn't get updated to reflect that the layer is now encrypted, and there's no clear path to undo encryption either.

On top of that, if I accidentally request encryption on a Docker-format manifest (which doesn't support encrypted layers at all), there's no error — it just silently proceeds with incorrect behavior. I'd expect it to fail loudly instead.

There's also a problem when converting between formats. If I have an encrypted OCI image and want to convert it to a Docker-compatible format, the conversion fails in unclear ways. The right behavior should be that a plain conversion of an encrypted image fails with an error (because the target format can't represent encrypted layers), but that it's possible to simultaneously decrypt and convert in a single operation.

I also need the reverse to work: when converting from a Docker format to OCI, it should be possible to request encryption of the layers as part of the conversion.

Finally, whatever conversion happens, the source image object itself should not be modified — after a conversion with decryption, the source should look exactly as it did before.

Could you add support for these encryption/decryption operations in the manifest layer update and format conversion paths? This includes creating any necessary test fixture files for encrypted OCI manifests.
