Simplify the HTTP filter configuration trait interface by removing the unused generic type parameter. Update the trait and related components to only require the per-request filter handle type, thereby reducing unnecessary boilerplate and improving clarity.

*   Modify the `HttpFilterConfig` trait:
    *   Accept a single generic type parameter `EHF` bounded by `EnvoyHttpFilter`.
    *   Remove the `EC` type parameter previously bounded by `EnvoyHttpFilterConfig`.
    *   Update the `new_http_filter` method to accept `&mut EHF` as its `envoy` argument.

*   Update the `NewHttpFilterConfigFunction` type alias:
    *   Change the return type to `Option<Box<dyn HttpFilterConfig<EHF>>>`.
    *   Remove the previous dual type parameter form `Option<Box<dyn HttpFilterConfig<EC, EHF>>>`.

*   Revise the `envoy_dynamic_module_on_http_filter_new_impl` function:
    *   Change the first parameter to `&mut EnvoyHttpFilterImpl`.
    *   Update the second parameter to `&mut dyn HttpFilterConfig<EnvoyHttpFilterImpl>`.

*   Ensure all concrete implementations of `HttpFilterConfig`:
    *   Use the new single-parameter form `impl<EHF: EnvoyHttpFilter> HttpFilterConfig<EHF> for StructName`.
    *   Modify the `new_http_filter` method to take `&mut EHF`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.