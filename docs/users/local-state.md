# Local State

SolarNode provides a persistent "state" database of Local State entities, for use by plugins and
[expressions](./expressions.md#local-state-functions). The [Local State management](./setup-app/settings/local-state.md)
page in SolarNode can be used to view and manage Local State entities.

A **Local State entity** is like a key-value pair. A **key** uniquely identifies each entity. It then has
an associated **value** of a specific **type** (for example number or string). The entity also tracks the date
it was first created, and the most recent modification date.

!!! tip

	See the [peak tracking recipe](../recipes/datum-filters/peak-tracking.md) for one example of how
	Local State can be used in SolarNode to track maximum property values (peaks) within a rolling
	time period.

## Boolean type

The `Boolean` type represents an on/off toggle state. It can be either `true` or `false`.

## Number types

SolarNode supports 3 different integer types and 3 different decimal (floating point) types.

| Type | Identifier | Description |
|:-----|:-----------|:------------|
| Integer        | `Integer` | An arbitrary precision integer. |
| 32-bit integer | `Int32` | A 32-bit signed integer. |
| 64-bit integer | `Int64` | A 64-bit signed integer. |
| Decimal        | `Decimal` | An arbitrary precision decimal (floating point). |
| 32-bit decimal | `Float2` | An IEEE-754 32-bit signed floating point. |
| 64-bit decimal | `Float64` | An IEEE-754 64-bit signed floating point. |

## String type

The `String` type is a UTF-8 encoded string. The maximum size is 8KB (UTF-8 bytes).

## Map type

The `Mapping` type holds an arbitrary key-value mapping. It is formatted as a JSON object and supports
the same value types as JSON: boolean, 64-bit floating point, and strings. For example:

```json
{"setpoint":19, "mode":"manual", "turbo":true}
```

!!! note "Map size limitiation"

	The Map type is stored internally as JSON, and its maximum size is 8KB (UTF-8 bytes).

