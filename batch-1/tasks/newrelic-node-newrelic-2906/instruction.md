Implement a bridge in the New Relic Node.js agent to translate OpenTelemetry spans into New Relic transaction segments. Ensure the bridge is gated by a feature flag and properly logs and tracks its setup and usage.

*   Add a feature flag:
    *   Name: `opentelemetry_bridge`
    *   Location: `lib/feature_flags.js`
    *   Default value: `false`
    *   Ensure `feature_flag.test.js` includes `'opentelemetry_bridge'` in the 'used' array.

*   Implement the bridge setup function:
    *   Location: `lib/otel/setup.js`
    *   Function signature: `setupOtel(agent, logger?) -> BasicTracerProvider | null | undefined`
    *   When `agent.config.feature_flag.opentelemetry_bridge` is `false`:
        *   Log the warning: '`feature_flag.opentelemetry_bridge` is not enabled, skipping setup of opentelemetry-bridge' using `logger`.
        *   Return `null` or `undefined`.
    *   When `agent.config.feature_flag.opentelemetry_bridge` is `true`:
        *   Return a `BasicTracerProvider` with:
            *   `provider.resource.attributes['service.name']` set to the agent's application name.
            *   `provider._config.spanLimits.attributeValueLengthLimit` set to `4095`.
        *   Record a supportability metric: `Supportability/Nodejs/OpenTelemetryBridge/Setup` with a call count of 1.

*   Modify context management:
    *   Export a `Symbol` named `otelSynthesis` from `lib/symbols.js`.
    *   Ensure the context object returned by `otel.context.active()`:
        *   Has properties `_transaction`, `_segment`, and `_otelCtx`.
        *   Implements methods `getValue(key)`, `setValue(key, value)`, and `deleteValue(key)`.
        *   On `setValue(key, value)`, if `value` has `otelSynthesis` with `segment` and `transaction`, set `_transaction` and `_segment` accordingly.

*   Adjust segment creation:
    *   Modify `createInternalSegment` in `lib/otel/segments/internal.js` to set the segment name directly to `otelSpan.name` without prefixing it with 'Custom/'.

*   Ensure span and metric handling:
    *   Internal spans produce scoped and unscoped metrics with a call count of 1.
    *   HTTP spans create `External` segments and metrics with standard naming conventions and a call count of 1.
    *   After `span.end()`, ensure the span object no longer has the `otelSynthesis` symbol property.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.