---
title: Control Updater
---
# Control Updater Datum Filter

The [Control Updater Datum Filter][src] provides a way to update controls with the result of an
expression, optionally populating the expression result as a datum property.

This filter is provided by the [Standard Datum Filters][sdf] plugin, which is provided by the
[`solarnode-app-datumfilters`][solarnode-app-datumfilters]
[package](../setup-app/system/packages.md) in SolarNodeOS.

## Settings

<figure markdown>
  ![Control Updater filter component settings](../../images/users/datum-filters/controlupdater-filter-settings%402x.png){width=528 loading=lazy}
  <caption markdown>The screen shot shows a filter that would toggle the `/power/switch/1` control on/off based on the
`frequency` property in the `/power/1` datum stream: on when the frequency is 50 or higher, off
otherwise.</caption>
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description                                                       |
|:-------------------|:------------------------------------------------------------------|
| Control Configurations | A list of control expression configurations. |

Each control configuration contains the following settings:

| Setting             | Description                                                       |
|:--------------------|:------------------------------------------------------------------|
| Control ID          | The ID of the control to update with the expression result. |
| Property            | The optional datum property to store the expression result in. |
| Property Type       | The datum property type to use. |
| Expression          | The expression to evaluate. See [below](#expressions) for more info. |
| Expression Language | The [expression language][expr] to write **Expression** in. |

## Expressions

See the [Expressions][expr] guide for general expressions reference. The root object
is a [`DatumExpressionRoot`][DatumExpressionRoot] that lets you treat all datum properties, and
filter parameters, as expression variables directly.

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[expr]: ../expressions.md
[DatumExpressionRoot]: https://github.com/SolarNetwork/solarnetwork-common/blob/develop/net.solarnetwork.common/src/net/solarnetwork/domain/DatumExpressionRoot.java
[Datum]: https://github.com/SolarNetwork/solarnetwork-common/blob/develop/net.solarnetwork.common/src/net/solarnetwork/domain/datum/Datum.java
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-ControlUpdater.md
