I'm working on enforcing a clean architectural boundary between the TUI layer and the core library in our Rust project.

*   The `codex-tui` crate must expose a `legacy_core` module accessible as `crate::legacy_core` from any file within that crate.

*   The `legacy_core::config` sub-module must re-export `Config`, `ConfigBuilder`, `Constrained`, and `ConstraintError` (the same types previously imported directly from the external core crate under an equivalent `config` module).

*   The `legacy_core::config_loader` sub-module must re-export `AppRequirementToml`, `AppsRequirementsToml`, `ConfigLayerStack`, `ConfigRequirements`, `ConfigRequirementsToml`, and `RequirementSource`.

*   The `legacy_core::plugins` sub-module must re-export `OPENAI_CURATED_MARKETPLACE_NAME` and `PluginCapabilitySummary`.

*   The `legacy_core::skills::model` sub-module must re-export `SkillMetadata`.

*   The `legacy_core::test_support` sub-module must re-export `construct_model_info_offline`, `all_model_presets`, and `get_model_offline`.

*   All source files within `codex-rs/tui` that previously imported types using the external core crate path must be updated to use `crate::legacy_core::*` paths instead.


*   Interface details: The implementation must introduce a `legacy_core` module that is accessible as `crate::legacy_core` from within the `codex-tui` crate (i.e., the `codex-rs/tui` package). This module serves as a re-export shim for types and utilities that were previously imported directly from the external core crate.

---

Type: Module
Name: legacy_core
Location: codex-rs/tui/src/lib.rs (re-exported so it is accessible as `crate::legacy_core`)
Description: A transitional compatibility module re-exporting core types and utilities. It must expose the following sub-modules, each re-exporting the corresponding items from the core crate.

Sub-module: `legacy_core::config`
Must re-export (at minimum):
- `Config`
- `ConfigBuilder`
- `Constrained`
- `ConstraintError`

Sub-module: `legacy_core::config_loader`
Must re-export (at minimum):
- `AppRequirementToml`
- `AppsRequirementsToml`
- `ConfigLayerStack`
- `ConfigRequirements`
- `ConfigRequirementsToml`
- `RequirementSource`

Sub-module: `legacy_core::plugins`
Must re-export (at minimum):
- `OPENAI_CURATED_MARKETPLACE_NAME`
- `PluginCapabilitySummary`

Sub-module: `legacy_core::skills` (and nested `legacy_core::skills::model`)
Must re-export (at minimum):
- `skills::model::SkillMetadata`

Sub-module: `legacy_core::test_support`
Must re-export (at minimum):
- `construct_model_info_offline`
- `all_model_presets`
- `get_model_offline`

---

Note: The golden.patch shows one approach — defining `legacy_core` inside the `codex-rs/app-server-client` crate and then re-exporting it with `pub(crate) use codex_app_server_client::legacy_core;` in `codex-rs/tui/src/lib.rs`. Any approach that makes `crate::legacy_core::{config, config_loader, plugins, skills, test_support}` accessible with the listed items is valid.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.