---
title: Unchanged Property
---
# Unchanged Property Filter

The [Unchanged Property Filter][src] provides a way to discard **individual datum properties** that
have not changed within a datum stream.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

!!! tip

	See the [Unchanged Datum Filter](./unchanged.md) for a filter that can discard entire unchanging
	_datum_ (at the source ID level).

## Settings

<figure markdown>
  ![Unchanged Property filter component settings](../../images/users/datum-filters/unchanged-property-filter-settings@2x.png){width=724 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description |
|:-------------------|:------------|
| Default Unchanged Max Seconds | When greater than `0` then the maximum number of seconds to discard unchanged properties within a single datum stream (source ID). Use this setting to ensure a property is included occasionally, even if the property value has not changed. Having at least one value per hour in a datum stream is recommended. This time period is always relative to the last unfiltered property within a given datum stream seen by the filter. |
| Property Configurations | A list of [property settings](#property-settings). |

## Property Settings

<figure markdown>
  ![Unchanged Property filter property settings](../../images/users/datum-filters/unchanged-property-filter-property-settings@2x.png){width=675 loading=lazy}
</figure>

Use the <kbd>+</kbd> and <kbd>-</kbd> buttons to add/remove Property configurations.

Each property source mapping configuration contains the following settings:

| Setting   | Description |
|:----------|:------------|
| Property           | A regular expression [pattern][regex] to match against datum property names. All matching properties will be filtered. |
| Unchanged Max Seconds | When greater than `0` then the maximum number of seconds to discard unchanged properties within a single datum stream (source ID). This can be used to override the filter-wide **Default Unchanged Max Seconds** setting, or left blank to use the default value. |

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-UnchangedProperty.md
