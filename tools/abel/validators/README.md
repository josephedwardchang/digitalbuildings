# ABEL Spreadsheet

### Overview
The ABEL Spreadsheet serves as a user-friendly interface for ABEL and allows users to create a
[Building Configuration file](../../../ontology/docs/building_config.md) from scratch.

***Note:*** ABEL can ingest Building Config files using the [initialize
operation](../../../ontology/docs/building_config.md#initialize) but it does not
currently support [update
operations](../../../ontology/docs/building_config.md#update)

### Generating GUIDs

There are two use cases for ABEL,
[brownfield](../../../ontology/docs/building_config.md#update) and
[greenfield](../../../ontology/docs/building_config.md#initialize). Currently ABEL
only supports greenfield buildings or Building Configs under the [Initialize
operation](../../../ontology/docs/building_config.md#initialize)

#### Greenfield building

A [greenfield building](../../../ontology/docs/building_config.md#initialize) 
is one that has yet to be onboarded, and ABEL is used to 
create a Building Config from scratch to model this building. In
this case **GUIDs do not need to be provided except for when there are
duplicate entity codes.** Then and only then will the user need to provide
GUIDs for the entities with duplicate codes in every one of the ABEL
spreadsheet tables.

GUIDs can be generated manually by using the `GUID` tab in the menu bar at the top 
of the ABEL spreadsheet template. ***Note:*** Upon using the `Generate GUIDs` 
function, the user will have to give the guid generation script permission to 
edit a user's spreadsheet.

![using guid tab](../../../ontology/docs/figures/tools/abel/abel_guid_tab.png?raw=true)

## Google Sheets template

The [ABEL Spreadsheet Template](https://docs.google.com/spreadsheets/d/1tcLjFnHiXUT-xh5C1hRKiUVaUH_CzgSI8zFQ_B8q7vs/copy#gid=980240783) has a strict format that must be adhered to.

Below is a definition of each table and column in the ABEL spreadsheet:
- [Site](#site)
- [Entities](#entities)
- [Entity Fields](#entity-fields)
- [States](#states)
- [Connections](#connections)

### Site

The `Site` tab holds all information about a project site or building.

`Building Code` *string* **required**

A [human-readable code](../../../ontology/docs/building_config.md#identifiers) for a building. e.g. `US-SEA-BLDG1`

`Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) [identifier](../../../ontology/docs/building_config.md#identifiers) for a building.
Unless you are onboarding a Google building, this value is generated by ABEL and does not need to be provided for initial onboarding.

***Note:*** A Site should not be considered an entity and should not be listed in
the `Entities` tab.

### Entities

The `Entities` tab holds all information about [reporting entities](../../../ontology/docs/building_config.md#reporting-physical-devices) and [virtual entities](../../../ontology/docs/building_config.md#virtual-devices) in a building or site. If ABEL is being used for Google then Guids are required for all entities in the `FACILTITIES` namespace which can be retrieved from DB API.

`Entity Code` *string* **required**

[Human-readable code](../../../ontology/docs/building_config.md#identifiers) for an entity e.g. `AHU-1001`

`Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random))
[identifier](../../../ontology/docs/building_config.md#identifiers) for an entity. **Note:** If an entity belongs to the `FACITITLIES` namespace and ABEL is
being used or Google, then this field is **required** or else spreadsheet
validation will fail.

`Etag` *string*

Version control tag for entities in an entity type database. Used only when updating an existing building config.

`Is Reporting` *Boolean* **required**

`TRUE` if an entity is [reporting data](../../../ontology/docs/building_config.md#reporting-physical-devices) and `FALSE` if [the entity is virtual](../../../ontology/docs/building_config.md#virtual-devices).

`Cloud Device ID` *string*

Cloud registry [device numeric identifier](../../../ontology/docs/building_config.md#identifiers). The device numeric ID is automatically created by a Cloud IoT Core (or similar cloud iot service), and it is globally unique and not editable.

`DBO Namespace` *string* **required**

[Namespace](../../../ontology/docs/ontology.md#namespaces) for an entity as defined in the [Digital Buidings Ontology](../../../ontology/README.md). e.g. `HVAC`

`DBO General Type` *string*

[General Type](../../../ontology/docs/model.md#general-types) for an entity as defined in a particular namespace in the [Digital Buildings Ontology](../../../ontology/README.md). e.g. `AHU`

`DBO Entity Type Name` *string* **required**

[Entity Type Name](../../../ontology.md#type-names) for an Entity as defined in the [Digital Buildings Ontology](../../../ontology/README.md). e.g. `VAV_SD_DSP`

### Entity Fields

The `Entity Fields` tab defines fields translated by [reporting entities](../../../ontology/docs/building_config.md#reporting-physical-devices) and linked to [virtual entities](../../../onotology/docs/building_config.md#virtual-devices) defined in the `Entities` tab.

`Entity Code` *string* **required**

A [human-readable code](../../../onotology/docs/building_config.md#identifiers) for an entity of which an entity field is a data point. This code must already be defined in the [Entities](#entities) table. e.g. `AHU-1001`

`Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) value for an entity corresponding to the entity in the Entity Code column ad is a unique [identifier for an entity](../../../onotology/docs/building_config.md#identifiers). This column does not need be filled in unless the corresponding entity code is **not unique**. This column will be used when ABEL writes to a blank spreadsheet.

`Reporting Entity Code` *string*

If a field is linked to a [virtual entity](../../../ontology/docs/building_config.md#virtual-devices) and its telemetry data is reported
by a [reporting entity](../../../ontology/docs/building_config.md#reporting-physical-devices), then the `Entity Code` column must be the virtual entity code and the `reporting entity code` is the code of the
entity reporting the field's data. This may be a gateway or some other IoT connected entity. This value may be left blank unless a field is linked to a [virtual entity](../../../ontology/docs/building_config.md#virtual-devices).

`Reporting Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) [identifier](../../../ontology/docs/building_config.md#identifiers) for an entity corresponding to the entity in the Reporting Entity Code Column. This value may be left blank unless the `reporting entity code` is **not unique**.

`DBO Standard Field Name` *string* **required**

[Standardized field name](../../../ontology/docs/ontology.md#fields) belonging to an entity type as defined in the [Digital Buildings Ontology](../../../ontology). e.g. `zone_air_temperature_sensor`

`Reporting Entity Field` *string*

Name of a field as it is reported by a [gateway or other reporting entity](../../../onotology/docs/building_config.md#reporting-physical-devices). e.g. `zone_air_temperature_sensor_1` where the standard field name is `zone_air_temperature_sensor`. If the a field is not linked to a virtual entity then this value does not need to be filled in. Reporting entity fields may be [enumerated](../../../ontology/docs/ontology.md#enumeration).

`Raw Field Path` *string* **required**

Path to data point on a physical device's telemetry payload. The field path is used as the `present_value` in an entity type's [translation](../../../ontology/docs/building_config.md#defining-translations) to a physical device payload. e.g. `points.discharge_fan_run_command.present_value`.

`Raw Unit Path` *string*

Path to a data point's units in a telemetry payload. Used as the `key` for `units` in an entity type [translation](../../../onotology/docs/building_config.md#defining-translations). e.g. `points.zone_air_cooling_temperature_setpoint.unit`. Leave blank or put `no-units` if a field is a [multi-state value](../../../ontology/docs/ontology.md#multi-state-values)

`DBO Standard Unit Value` *string*

[Standardized dimensional unit](../../../ontology/docs/ontology.md#dimensional-units) for a standard field as defined in [Digital Buildings Ontology](../../../ontology/yaml/resources/fields). e.g. `degrees_celsius`

`Raw Unit Value` *string*

Unit value as reported by a [reporting
device](../../../onotology/docs/building_config.md#reporting-physical-devices). Used for a [dimensional unit's](../../../ontology/docs/ontology.md#dimensional-units) value mapping in an entity [translation](../../../ontology/docs/building_config.md#defining-translations) e.g. `degC`

### States

The `States` tab defines values for [multi-state value](../../../ontology/docs/building_config.md#multi-state-values) fields defined in the `Entity
Fields` tab.

`Entity Code` *string* **required**

If a field is linked to a [virtual entity](../../../ontology/docs/building_config.md#virtual-devices) and its telemetry data is reported
by some [reporting entity](../../../ontology/docs/building_config.md#reporting-physical-devices), then the `Entity Code` column must be the virtual entity code and the `reporting entity code` is the code of the
entity reporting the field's data. This may be a gateway or some other IoT connected entity.

`Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) [identifier](../../../ontology/docs/building_config.md#identifiers) for an entity corresponding to the entity in the Entity Code column.

`Reporting Entity Field` *string* **required**

Name of the [entity field](#entity-fields) which [reports](../../../ontology/docs/building_config.md#reporting-physical-devices) a [multi-state value](../../../ontology/docs/ontology.md#multi-state-values) in its telemetry payload.

`DBO Standard State` *string* **required**

Standardized [multi-state value](../../../ontology/docs/ontology.md#multi-state-values) name as defined in [Digital Buildings Ontology](https://github.com/google/digitalbuildings/blob/master/ontology/yaml/resources/states/states.yaml). e.g. `ON`

`Raw State` *string* **required**

Raw [multi-state value](../../../../../../ontology/docs/ontology.md#multi-state0-values) for a data point in the telemetry payload for a device. e.g. `on`

### Connections

Connections define the [device
relationships](../../../ontology/docs/building_config.md#device-relationships) that exist between two entities
in a building. Connections to a building such as a building having a `CONTAINS`
relationship to a floor do not need to be defined as they are implied. For
Google buildings, relationships to a building are already accounted for and do
not need to be re-defined.

`Source Entity Code` *string* **required**

[Human-readable code](../../../ontology/docs/building_config.md#identifiers) for a connection source entity. e.g. `AHU-1001`

`Source Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) [identifier](../../../ontology/docs/building_config.md#identifiers) for an entity corresponding to an entity in the `Entities` tab for connection source. This does not need to be provided **unless the source entity code is not unique**.

`DBO Connection Type` *string* **required**

A connection type as defined in [Digital Buildings Ontology connections](../../../ontology/yaml/resources/connections/connections.yaml). e.g. `FEEDS`

`Target Entity Code` *string* **required**

[Human-readable code](../../../ontology/docs/building_config.md#identifiers) for a connection target entity. e.g. `AHU-1001`

`Target Entity Guid` *string*

[UUID4](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)) [identifier](../../../ontology/docs/building_config.md#identifiers) for an entity corresponding to an entity in the `Entities` tab for a connection target. This does not need to be provided **unless the target entity code is non-unique**.