I'm working on extending the battery component in Home Assistant to support automation triggers, not just conditions.

*   The battery component must include a trigger module at homeassistant/components/battery/trigger.py that exposes an async_get_triggers(hass) coroutine returning a dictionary mapping trigger names to trigger implementations.

*   The async_get_triggers function must return a dict containing exactly these keys: "low", "not_low", "started_charging", "stopped_charging", "level_changed", and "level_crossed_threshold".

*   The "battery" platform name must be added to the _EXPERIMENTAL_TRIGGER_PLATFORMS set in homeassistant/components/automation/__init__.py so that all battery triggers are gated behind the labs/preview features flag.

*   All six battery trigger keys (battery.low, battery.not_low, battery.started_charging, battery.stopped_charging, battery.level_changed, battery.level_crossed_threshold) must be unavailable unless the labs preview features flag is enabled.

*   The "low" trigger must watch binary sensor entities whose device class is "battery" and fire when such an entity transitions to STATE_ON (low state). It must not fire on transitions from None (no previous state).

*   The "not_low" trigger must watch binary sensor entities whose device class is "battery" and fire when such an entity transitions to STATE_OFF. It must not fire on transitions from None.

*   The "started_charging" trigger must watch binary sensor entities whose device class is "battery_charging" and fire when such an entity transitions to STATE_ON. It must not fire on transitions from None.

*   The "stopped_charging" trigger must watch binary sensor entities whose device class is "battery_charging" and fire when such an entity transitions to STATE_OFF. It must not fire on transitions from None.

*   The binary sensor triggers ("low", "not_low", "started_charging", "stopped_charging") must each support "any", "first", and "last" behavior options controlling multi-device firing semantics.

*   The "level_changed" trigger must watch sensor entities with device class "battery" and unit of measurement "%", as well as number entities with device class "battery" and unit of measurement "%", and fire when the numerical value changes. It must support the "any" behavior option.

*   The "level_crossed_threshold" trigger must watch sensor entities with device class "battery" and unit of measurement "%", as well as number entities with device class "battery" and unit of measurement "%", and fire when the numerical value crosses a configured threshold. It must support "any", "first", and "last" behavior options.


*   Interface details: Type: Function
Name: async_get_triggers
Location: homeassistant/components/battery/trigger.py
Signature: async_get_triggers(hass: HomeAssistant) -> dict[str, type[Trigger]]
Description: Coroutine that returns a dictionary mapping battery trigger names to their trigger implementations. Must return a dict containing the keys: "low", "not_low", "started_charging", "stopped_charging", "level_changed", and "level_crossed_threshold".

---

Type: Registration
Name: _EXPERIMENTAL_TRIGGER_PLATFORMS
Location: homeassistant/components/automation/__init__.py
Description: The string "battery" must be added to the _EXPERIMENTAL_TRIGGER_PLATFORMS set so that all battery triggers are gated by the labs/preview features flag.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.