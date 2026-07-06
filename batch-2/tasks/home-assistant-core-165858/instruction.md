I'm working on the Growatt Server integration and noticed it's using a non-standard approach for controlling how many decimal places sensor readings show.

*   The GrowattSensorEntityDescription dataclass must not define a custom precision field. The existing custom precision field (named `precision`) must be removed, and the standard `suggested_display_precision` field inherited from SensorEntityDescription must be used instead for controlling display precision.

*   The GrowattRequiredKeysMixin class must be removed. The `api_key: str` field must be defined directly on GrowattSensorEntityDescription.

*   GrowattSensor's native_value property must return the raw value from the coordinator without applying any manual rounding. The manual rounding logic that previously checked for a `precision` field must be removed.

*   All sensor definitions in the TLX device type must replace `precision=N` with `suggested_display_precision=N`. All TLX sensors (energy, power, current, voltage, frequency) must use suggested_display_precision=1.

*   All sensor definitions in the inverter device type must replace `precision=N` with `suggested_display_precision=N`. Most inverter sensors use suggested_display_precision=1 (energy, power, current, voltage, frequency). The one exception is the first voltage input sensor (key inverter_voltage_input_1) which must use suggested_display_precision=2.

*   All sensor definitions in the storage device type must replace `precision=N` with `suggested_display_precision=N`. Storage-specific sensors must use suggested_display_precision=2: AC input frequency, AC input voltage, AC output frequency, battery voltage, output voltage, PV1 and PV2 charging voltages, and all current sensors (battery current, grid current, etc.).

*   The 'load percentage' sensor in the storage device type (key storage_load_percentage, battery device class) must have suggested_display_precision=2 added. Previously this sensor had a custom precision field but no standard suggested_display_precision, so it showed no precision in entity options.

*   All sensor definitions for the min device type (tested with the v1 API) must replace `precision=N` with `suggested_display_precision=N`. All min device sensors use suggested_display_precision=1.

*   All total/aggregate sensor definitions must replace `precision=N` with `suggested_display_precision=N`. Energy total sensors must use suggested_display_precision=1.

*   The suggested_display_precision value must appear in the sensor entity's options under the 'sensor' key (i.e., options['sensor']['suggested_display_precision']), consistent with how Home Assistant's SensorEntityDescription exposes this standard field.


*   Interface details: Type: Class
Name: GrowattSensorEntityDescription
Location: homeassistant/components/growatt_server/sensor/sensor_entity_description.py
Description: Dataclass describing a Growatt sensor entity. Must NOT define a custom `precision` field. Must use the standard `suggested_display_precision` field inherited from `SensorEntityDescription` to declare display precision. Must define `api_key: str` as a required field directly on this class (not via a mixin). Must use `@dataclass(frozen=True, kw_only=True)` decorator. The `GrowattRequiredKeysMixin` class should be removed entirely; `api_key` is defined directly on `GrowattSensorEntityDescription`.
Signature: GrowattSensorEntityDescription(api_key: str, suggested_display_precision: int | None = None, currency: bool = False, previous_value_drop_threshold: float | None = None, never_resets: bool = False, ...)

Type: Class
Name: GrowattSensor
Location: homeassistant/components/growatt_server/sensor/__init__.py
Description: Sensor entity class. The `native_value` property must NOT manually round values using a `precision` field. It must return the raw value from the coordinator as-is.
Signature: native_value(self) -> StateType | date | datetime

Type: Variable
Name: INVERTER_SENSOR_TYPES
Location: homeassistant/components/growatt_server/sensor/inverter.py
Description: Tuple of GrowattSensorEntityDescription instances for the inverter device type. All sensor definitions must use `suggested_display_precision=N` instead of `precision=N`. Values: energy sensors (kWh)=1, most power sensors (W)=1, current sensors (A)=1, most voltage sensors (V)=1, frequency sensors (Hz)=1. Exception: the first voltage input sensor (key "inverter_voltage_input_1") must use suggested_display_precision=2.

Type: Variable
Name: STORAGE_SENSOR_TYPES
Location: homeassistant/components/growatt_server/sensor/storage.py
Description: Tuple of GrowattSensorEntityDescription instances for the storage device type. All sensor definitions must use `suggested_display_precision=N` instead of `precision=N`. Storage-specific measurements must use precision=2: AC input frequency, AC input voltage, AC output frequency, battery voltage, output voltage, PV1 charging voltage (key "storage_pv_charging_voltage"), PV2 charging voltage (key "storage_pv_charging_voltage_2"), and all current sensors. The "load percentage" sensor (key "storage_load_percentage", battery device class) must have suggested_display_precision=2 added — it previously had a custom precision field but no standard suggested_display_precision.

Type: Variable
Name: TLX_SENSOR_TYPES
Location: homeassistant/components/growatt_server/sensor/tlx.py
Description: Tuple of GrowattSensorEntityDescription instances for the TLX device type. All sensor definitions must use `suggested_display_precision=N` instead of `precision=N`. All TLX sensors use suggested_display_precision=1 (energy, power, current, voltage, frequency sensors).

Type: Variable/Tuple (for min/v1 API sensor types)
Location: homeassistant/components/growatt_server/sensor/ (relevant file for min device type)
Description: Sensor definitions for the min device type (tested via v1 API). All sensor definitions must use `suggested_display_precision=N` instead of `precision=N`. All sensors use suggested_display_precision=1 (energy, power, current, voltage, frequency sensors).

Type: Variable/Tuple (for total/aggregate sensor types)
Location: homeassistant/components/growatt_server/sensor/ (relevant file for total sensors)
Description: Total/aggregate sensor definitions. All energy sensors must use suggested_display_precision=1 instead of the previous custom precision field.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.