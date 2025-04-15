# Local State

SolarNode provides a persistent "state" database for use by plugins and [expressions](../../expressions.md#local-state-functions). The **Settings > Local State** page lets you manage this database. The term
"local" refers to the fact that the persisted state database is specific (_local_) to each node, and not published
to the SolarNetwork cloud.

<figure markdown>
![SolarNode Local State UI](../../../images/users/local-state/solarnode-local-state-ui@2x.png){width="1024" loading=lazy}
</figure>

## Local State entities

A **Local State** entity is like a key-value pair. A **key** uniquely identifies each entity. It then has
an associated _value_ of a specific _type_ (for example number or string). The entity also tracks the date
it was first created, and the most recent modification date.

### Boolean type

The `Boolean` type represents an on/off toggle state. It can be either `true` or `false`.

### Number types

SolarNode supports 3 different integer types and 3 different decimal (floating point) types.

| Type | Identifier | Description |
|:-----|:-----------|:------------|
| Integer        | `Integer` | An arbitrary precision integer. |
| 32-bit integer | `Int32` | A 32-bit signed integer. |
| 64-bit integer | `Int64` | A 64-bit signed integer. |
| Decimal        | `Decimal` | An arbitrary precision decimal (floating point). |
| 32-bit decimal | `Float2` | An IEEE-754 32-bit signed floating point. |
| 64-bit decimal | `Float64` | An IEEE-754 64-bit signed floating point. |

### String type

The `String` type is a UTF-8 encoded string. The maximum size is 8KB (UTF-8 bytes).

### Map type

The `Mapping` type holds an arbitrary key-value mapping. It is formatted as a JSON object and supports
the same value types as JSON: boolean, 64-bit floating point, and strings. For example:

```json
{"setpoint":19, "mode":"manual", "turbo":true}
```

!!! note "Map size limitiation"

	The Map type is stored internally as JSON, and its maximum size is 8KB (UTF-8 bytes).


## Managing Local State

Click the **+ Create Local State** button to create a new local state entity, or click on the **Key** value of
any existing state entity to edit it. An edit dialog will be shown where you can enter the details.

<figure markdown>
![Edit Local State entity](../../../images/users/local-state/solarnode-local-state-edit-modal@2x.png){width="1024" loading=lazy}
</figure>

To **delete** a Local State entity, click on its link to open the edit dialog, then click the **Delete** button.
