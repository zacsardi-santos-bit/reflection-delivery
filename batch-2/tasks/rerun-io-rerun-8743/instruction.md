Upgrade the GPU rendering library used by the renderer crate to the latest major version, addressing all breaking changes throughout the codebase. Ensure that the rendering context constructor accepts GPU device and command queue objects directly by value, update type names for texture and buffer copy operations, and adjust initialization and utility functions to the new API.

Requirements:

*   Update `RenderContext::new` function:
    *   Accept `device` and `queue` parameters by value as `wgpu::Device` and `wgpu::Queue`.
    *   Change `device` and `queue` fields on `RenderContext` struct to `wgpu::Device` and `wgpu::Queue`.

*   Modify `Cargo.toml` files:
    *   Upgrade `wgpu` dependency to version 24.0 in `crates/viewer/re_renderer/Cargo.toml` and workspace root `Cargo.toml`.
    *   Replace `wgpu-core` with `wgpu-types` at version 24.0 in both files.

*   Rename types throughout the `re_renderer` crate:
    *   `wgpu::ImageCopyTexture` to `wgpu::TexelCopyTextureInfo`.
    *   `wgpu::ImageCopyBuffer` to `wgpu::TexelCopyBufferInfo`.
    *   `wgpu::ImageDataLayout` to `wgpu::TexelCopyBufferLayout`.

*   Specific file updates:
    *   In `chunk_decoder.rs`, rename `wgpu::ImageCopyExternalImage` to `wgpu::CopyExternalImageSourceInfo` and replace `wgpu::ImageCopyTextureTagged` with `wgpu_types::CopyExternalImageDestInfo`.
    *   In `wgpu_core_error.rs`, add `use wgpu::core as wgpu_core;` to resolve `wgpu_core::error::ContextError` and `wgpu_core::command::CommandEncoderError`.
    *   In `wgpu_error_scope.rs`, remove `use std::sync::Arc;` and change device field and `start` parameter from `Arc<wgpu::Device>` to `wgpu::Device`.

*   Update `config.rs`:
    *   Modify `instance_descriptor` to remove `dx12_shader_compiler` and `gles_minor_version` fields; add `backend_options: wgpu::BackendOptions::default()`.
    *   Replace `wgpu::util::backend_bits_from_env()` with `wgpu::Backends::from_env()`.
    *   Ensure `.with_env()` is called on the completed struct.

*   Adjust `wgpu::Instance::new` calls:
    *   Pass a reference to the descriptor (`&descriptor`) instead of an owned value.

*   Ensure `RenderContext::new_test()` compiles successfully:
    *   Create device and queue without `Arc` wrapping and pass them directly to `Self::new`.
    *   Pass the result of `config::testing_instance_descriptor()` by reference to `wgpu::Instance::new`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.