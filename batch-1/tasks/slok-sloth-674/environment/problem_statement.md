## Description

Services that experience seasonal or cyclical traffic patterns — for example, high request rates during business hours and very low rates at night — often suffer from unreliable SLO burn-rate alerts. Under standard SLO recording rules, every time window is treated with equal weight regardless of the underlying traffic volume. During quiet periods, even a small number of failures can make the burn rate appear extremely high, causing false alerts and noisy pages.

A new contrib plugin is needed that corrects this behavior by applying a volume-based scaling factor to the SLI error ratio. Instead of computing the error ratio purely from failures over a time window, the plugin scales the numerator based on how much traffic occurred in that window relative to the overall SLO period. Higher-traffic windows contribute proportionally more to the burn rate signal, while low-traffic windows contribute less.

## Expected Behavior

- The plugin must only support services using the events-based SLI type (error query + total query). Attempting to use a raw SLI type should result in an error.
- The plugin must validate that the SLI query templates are well-formed and reference only supported template variables; invalid or unrecognized template variables should result in an error.
- For valid SLOs, the plugin should replace the standard SLI error recording rules with corrected versions that incorporate the volume-scaling factor.
- The plugin should also emit a set of new metadata recording rules that capture the correction factor for each alerting time window and for the total SLO time window.

## Why This Matters

Teams running services with seasonal traffic patterns currently cannot rely on standard SLO alerting without manual workarounds. This plugin provides an automatic, configurable way to normalize burn-rate calculations against traffic volume, making SLO alerts more reliable and actionable regardless of time-of-day traffic variation.
