---
title: Operational Mode
---
# Operational Mode Datum Filter

The [Operational Mode Datum Filter][src] provides a way to evaluate [expressions][expr] to toggle
[operational modes][opmodes]. When an expression evaluates to `true` the associated operational mode
is activated. When an expression evaluates to `false` the associated operational mode is
deactivated.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Settings

<figure markdown>
  ![Operational Mode filter component settings](../../images/users/datum-filters/opmode-filter-settings%402x.png){width=616 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting             | Description                                                       |
|:--------------------|:------------------------------------------------------------------|
| Expressions         |  A list of expression configurations that are evaluated to toggle operational modes. |

Use the <kbd>+</kbd> and <kbd>-</kbd> buttons to add/remove expression configurations.

## Expression settings

Each expression configuration contains the following settings:

| Setting             | Description                                                       |
|:--------------------|:------------------------------------------------------------------|
| Mode                | The operational mode to toggle. |
| Expire Seconds      | If configured and greater than `0`, the number of seconds after activating the operational mode to automatically deactivate it. If not configured or `0` then the operational mode will be deactivated when the expression evaluates to `false`. See [below](#expire-setting) for more information. |
| Property            | If configured, the datum property to store the expression result in. See [below](#property-setting) for more information. |
| Property Type       | The datum property type to use if **Property** is configured. See [below](#property-setting) for more information. |
| Expression          | The expression to evaluate. See [below](#expressions) for more info. |
| Expression Language | The [expression language][expr] to write **Expression** in. |

### Expire setting

When configured the expression will **never deactivate the operational mode directly**. When
evaluating the given expression, if it evaluates to `true` the mode will be activated and configured
to deactivate after this many seconds. If the operation mode was already active, the expiration will
be **extended** by this many seconds.

This configuration can be thought of like a time out as used on motion-detecting lights: each time
motion is detected the light is turned on (if not already on) and a timer set to turn the light off
after so many seconds of no motion being detected.

Note that the operational modes service might actually deactivate the given mode a short time
**after** the configured expiration.

### Property setting

A property does not have to be populated. If you provide a **Property** name to populate, the value
of the datum property depends on property type configured:

| Type          | Description |
|:--------------|:------------|
| Instantaneous | The property value will be `1` or `0` based on `true` and `false` expression results. |
| Status        | The property will be the expression result, so `true` or `false`. |
| Tag           | A tag named as the configured property will be added if the expression is `true`, or removed if `false`. |


## Expressions

See the [Expressions][expr] section for general expressions reference. The expression
must evaluate to  a **boolean** (`true` or `false`) result. When it evaluates to `true` the
configured operational mode will be **activated**. When it evaluates to `false` the operational mode
will be **deactivated** (unless an [expire setting](#expire-setting) has been configured).

The root object is a [datum samples expression object][DatumSamplesExpressionRoot] that lets you
treat all datum properties, and filter parameters, as expression variables directly, along with
the following properties:

| Property | Type | Description |
|:---------|:-----|:------------|
| `datum` | `GeneralNodeDatum` | A [`GeneralNodeDatum`][GeneralNodeDatum] object, populated with data from all property and virtual meter configurations. |
| `props` | `Map<String,Object>` | Simple Map based access to the properties in `datum`, and transform parameters, to simplify expressions. |

The following methods are available:

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `has(name)` | `String` | `boolean` | Returns `true` if a property named `name` is defined. |

### Expression examples

Assuming a datum sample with properties like the following:

| Property | Value |
|:---------|:------|
| `current` | `7.6`   |
| `voltage` | `240.1` |
| `status`  | `Error` |

Then here are some example expressions and the results they would produce:

| Expression | Result | Comment |
|:-----------|:-------|:--------|
| `voltage * current > 1800` | `true` | Since `voltage * current` is **1824.76**, the expression is `true`. |
| `status != 'Error'` | `false` | Since `status` is `Error` the expression is `false`. |


--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[expr]: ../expressions.md
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-OpMode.md
[DatumSamplesExpressionRoot]: https://github.com/SolarNetwork/solarnetwork-common/blob/develop/net.solarnetwork.common/src/net/solarnetwork/domain/DatumSamplesExpressionRoot.java
[GeneralNodeDatum]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node/src/net/solarnetwork/node/domain/GeneralNodeDatum.java
