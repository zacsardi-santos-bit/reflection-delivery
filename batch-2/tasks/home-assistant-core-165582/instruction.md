I'm working on improving the validation in the energy configuration module.

*   A statistic ID is considered 'external' if it does not conform to the Home Assistant entity ID format (i.e., it is not a valid `domain.entity_name` string). IDs such as `opower:utility_elec_12345_energy_consumption` or `external:grid_export` are external statistics.

*   FLOW_FROM_GRID_SOURCE_SCHEMA must raise vol.Invalid with a message containing 'not supported for external statistics' when `stat_energy_from` is an external statistic, `stat_cost` is not set, and either `entity_energy_price` or `number_energy_price` is not None.

*   FLOW_FROM_GRID_SOURCE_SCHEMA must accept configurations where `stat_energy_from` is an external statistic and `stat_cost` is provided (with or without price fields also set). When `stat_cost` is provided alongside price fields for an external statistic, validation must succeed.

*   FLOW_FROM_GRID_SOURCE_SCHEMA must accept configurations where `stat_energy_from` is an external statistic and no cost fields are configured at all (all price/cost fields are None or absent).

*   FLOW_TO_GRID_SOURCE_SCHEMA must raise vol.Invalid with a message containing 'not supported for external statistics' when `stat_energy_to` is an external statistic, `stat_compensation` is not set, and either `entity_energy_price` or `number_energy_price` is not None.

*   FLOW_TO_GRID_SOURCE_SCHEMA must accept configurations where `stat_energy_to` is an external statistic and `stat_compensation` is provided, even if price fields such as `number_energy_price` are also set.

*   ENERGY_SOURCE_SCHEMA (for grid type entries) must raise vol.Invalid with a message containing 'not supported for external statistics' when `stat_energy_from` is an external statistic, `stat_cost` is not set, and `entity_energy_price` is provided.

*   ENERGY_SOURCE_SCHEMA (for grid type entries) must raise vol.Invalid with a message containing 'not supported for external statistics' when `stat_energy_to` is an external statistic, `stat_compensation` is not set, and `number_energy_price_export` is provided.

*   ENERGY_SOURCE_SCHEMA (for grid type entries) must accept configurations where `stat_energy_from` is an external statistic and `stat_cost` is provided, and must also accept such entries where a price field like `entity_energy_price` is additionally set alongside `stat_cost`.

*   GAS_SOURCE_SCHEMA must raise vol.Invalid with a message containing 'not supported for external statistics' when `stat_energy_from` is an external statistic, `stat_cost` is not set, and either `entity_energy_price` or `number_energy_price` is not None.

*   GAS_SOURCE_SCHEMA must accept configurations where `stat_energy_from` is an external statistic and `stat_cost` is provided, even if `entity_energy_price` is also set.

*   WATER_SOURCE_SCHEMA must raise vol.Invalid with a message containing 'not supported for external statistics' when `stat_energy_from` is an external statistic, `stat_cost` is not set, and either `entity_energy_price` or `number_energy_price` is not None.

*   WATER_SOURCE_SCHEMA must accept configurations where `stat_energy_from` is an external statistic and `stat_cost` is provided, even if `number_energy_price` is also set.


*   Interface details: The following existing module-level schema objects in `homeassistant/components/energy/data.py` must be modified to add validation for external statistics. They are exported from this module and called directly by tests.

Type: Schema (module-level variable)
Name: FLOW_FROM_GRID_SOURCE_SCHEMA
Location: homeassistant/components/energy/data.py
Description: Validates a "flow from grid" energy source entry. Must be extended to reject `entity_energy_price` and `number_energy_price` when `stat_energy_from` is an external statistic and no `stat_cost` is provided. Must still accept these price fields when `stat_cost` is set alongside an external statistic.

Type: Schema (module-level variable)
Name: FLOW_TO_GRID_SOURCE_SCHEMA
Location: homeassistant/components/energy/data.py
Description: Validates a "flow to grid" (export) energy source entry. Must be extended to reject `entity_energy_price` and `number_energy_price` when `stat_energy_to` is an external statistic and no `stat_compensation` is provided. Must still accept these price fields when `stat_compensation` is set alongside an external statistic.

Type: Schema (module-level variable)
Name: GAS_SOURCE_SCHEMA
Location: homeassistant/components/energy/data.py
Description: Validates a gas energy source entry. Must be extended to reject `entity_energy_price` and `number_energy_price` when `stat_energy_from` is an external statistic and no `stat_cost` is provided. Must still accept these price fields when `stat_cost` is set alongside an external statistic.

Type: Schema (module-level variable)
Name: WATER_SOURCE_SCHEMA
Location: homeassistant/components/energy/data.py
Description: Validates a water energy source entry. Must be extended to reject `entity_energy_price` and `number_energy_price` when `stat_energy_from` is an external statistic and no `stat_cost` is provided. Must still accept these price fields when `stat_cost` is set alongside an external statistic.

Type: Schema (module-level variable)
Name: ENERGY_SOURCE_SCHEMA
Location: homeassistant/components/energy/data.py
Description: Top-level schema for all energy source types. For grid type entries, must reject price fields when the corresponding statistic (import or export) is an external statistic and no cost statistic is provided. The grid source validation for import uses `stat_energy_from` / `entity_energy_price` / `stat_cost`; for export uses `stat_energy_to` / `entity_energy_price_export` / `number_energy_price_export` / `stat_compensation`.

Note on "external statistic" detection: A statistic ID is considered external if it is NOT a valid Home Assistant entity ID. Valid entity IDs follow the `domain.entity_name` format (with a period separator). IDs such as `"opower:utility_elec_12345_energy_consumption"` or `"external:grid_export"` (using a colon) are external statistics.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.