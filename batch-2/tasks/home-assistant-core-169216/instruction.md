I'm working on the UniFi Protect integration for Home Assistant and need to add support for physical siren devices accessible through the public API.

*   When api.has_public_bootstrap is False, async_setup_entry must return immediately without creating any siren entities (zero entities of the siren platform type).

*   When api.has_public_bootstrap is True, async_setup_entry must create one ProtectSiren entity per entry in api.public_bootstrap.sirens; each entity's unique_id must be '{siren.mac}_siren'.

*   ProtectSiren initial state must reflect siren.is_active: False means the entity is off, True means the entity is on.

*   The turn-on service must call siren.play(duration=None) when no duration is specified.

*   The turn-on service must map valid integer durations to their corresponding enum values before calling play: 5 seconds → SirenDuration.FIVE, 10 → SirenDuration.TEN, 20 → SirenDuration.TWENTY, 30 → SirenDuration.THIRTY.

*   The turn-on service must validate the duration before making any API calls. An unsupported duration (e.g., 15 seconds) must raise ServiceValidationError; neither set_volume nor play must be called when an invalid duration is provided.

*   When a volume level (0.0–1.0) is provided to the turn-on service alongside a valid (or absent) duration, the entity must call siren.set_volume(round(volume_level * 100)) — for example, 0.75 → set_volume(75) — before calling play.

*   The turn-off service must call siren.stop(), cancel any pending auto-off timer, and immediately set the entity state to off without waiting for a server event.

*   API errors (NotAuthorized or ClientError) raised by the underlying siren object during turn-on must be caught and re-raised as HomeAssistantError.

*   The turn-on service must raise HomeAssistantError if the siren is no longer present in the public bootstrap (public_bootstrap.sirens is empty or does not contain the siren id).

*   The turn-off service must raise HomeAssistantError if has_public_bootstrap is False or the siren is no longer in the bootstrap.

*   The entity must become unavailable when the WebSocket connection is disconnected and recover (return to its normal on/off state) when the connection is re-established.

*   When a WS update arrives with siren_status.is_active=True and both siren_status.activated_at and siren_status.duration are set, the entity must schedule an auto-off callback at delay = (siren_status.activated_at + siren_status.duration) / 1000 - utcnow().timestamp() seconds. If delay <= 0, the entity must immediately set its state to off without scheduling a timer.

*   When the auto-off timer fires, the entity state must transition to off.

*   A manual turn-off must cancel the pending auto-off timer so the state remains off even after the original timer deadline passes.

*   At startup (async_added_to_hass), if the siren is already active with a timed run in progress, the entity must schedule the auto-off timer immediately rather than waiting for the next WS update.

*   When a WS delete event is received (the siren has been removed from the public bootstrap), the entity must become unavailable.

*   ProtectData must expose an async_subscribe_siren(mac, callback) method that registers a per-mac callback for siren updates and returns an unsubscribe callable.

*   The public devices WS message handler in ProtectData must route messages whose model type is ModelType.SIREN to siren subscribers; on a delete event (new_obj is None), it must dispatch the last-known siren object (old_obj) to subscribers.


*   Interface details: Type: Function
Name: async_setup_entry
Location: homeassistant/components/unifiprotect/siren.py
Signature: async_setup_entry(hass: HomeAssistant, entry: UFPConfigEntry, async_add_entities: AddConfigEntryEntitiesCallback) -> None
Description: Platform setup entry point for the UniFi Protect siren platform. Creates ProtectSiren entities from api.public_bootstrap.sirens only when api.has_public_bootstrap is True. If has_public_bootstrap is False, returns immediately without adding any entities.

Type: Class
Name: ProtectSiren
Location: homeassistant/components/unifiprotect/siren.py
Description: Home Assistant SirenEntity representing a UniFi Protect siren device accessed via the public API.
Signature:
  __init__(self, data: ProtectData, siren: Siren) -> None
    - Sets unique_id to f"{siren.mac}_siren"
    - Stores siren id as self._siren_id to look up the current siren from public_bootstrap.sirens
    - Stores siren mac as self._siren_mac for WS subscription
    - Reflects siren.is_active as is_on state
  async_turn_on(self, **kwargs: Any) -> None
    - Raises HomeAssistantError if the siren is no longer in the public bootstrap
    - Validates duration (from ATTR_DURATION) against SirenDuration enum values BEFORE any API call
    - Raises ServiceValidationError for unsupported durations (e.g., 15); when raised, neither set_volume nor play is called
    - Valid duration seconds: 5 → SirenDuration.FIVE, 10 → SirenDuration.TEN, 20 → SirenDuration.TWENTY, 30 → SirenDuration.THIRTY
    - If volume_level (from ATTR_VOLUME_LEVEL) is given, calls siren.set_volume(round(volume_level * 100)) (e.g., 0.75 → set_volume(75))
    - Calls siren.play(duration=<SirenDuration or None>)
    - API errors (NotAuthorized, ClientError) are wrapped and re-raised as HomeAssistantError
  async_turn_off(self, **kwargs: Any) -> None
    - Raises HomeAssistantError if the siren is no longer available (has_public_bootstrap False or siren not in bootstrap)
    - Calls siren.stop()
    - Cancels any pending auto-off timer
    - Immediately sets is_on to False and writes state (optimistic update since server does not emit a WS stop event)
  async_added_to_hass(self) -> None
    - Subscribes to siren updates via data.async_subscribe_siren(self._siren_mac, self._async_updated)
    - At startup, if the siren is already active with a timed run, schedules the auto-off timer immediately
  _async_updated(self, siren: Siren) -> None
    - Callback invoked on WS update for this siren's mac
    - Cancels any previous auto-off timer
    - If siren is no longer in the public bootstrap (delete event): sets available=False, is_on=False
    - Schedules auto-off timer using delay = (siren_status.activated_at + siren_status.duration) / 1000 - utcnow().timestamp()
    - If delay <= 0 (already expired): sets is_on=False immediately without scheduling timer
    - Only writes state when availability or is_on changes

Type: Method
Name: async_subscribe_siren
Location: homeassistant/components/unifiprotect/data.py (on class ProtectData)
Signature: async_subscribe_siren(self, mac: str, update_callback: Callable[[Siren], None]) -> CALLBACK_TYPE
Description: Registers a callback to be invoked when a siren update is dispatched for the given MAC address. Returns an unsubscribe callable. The _async_process_devices_message method in ProtectData must route messages with model type ModelType.SIREN to these subscribers, and also dispatch the last-known Siren object (old_obj) to subscribers when a delete event (new_obj=None) is received.

Note: const.py must include Platform.SIREN in the PLATFORMS list and ModelType.SIREN in DEVICES_WS_SUBSCRIBED_MODELS so the siren platform is loaded and WS messages for sirens are received.

Note: The mock UFP client in tests/components/unifiprotect/conftest.py must have client.has_public_bootstrap = False set by default so existing tests are not broken by the new siren setup code.

Note: The _make_public_bootstrap helper in tests/components/unifiprotect/test_alarm_control_panel.py must include pb.sirens = {} in the PublicBootstrap mock so that the siren setup code can access public_bootstrap.sirens without AttributeError.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.