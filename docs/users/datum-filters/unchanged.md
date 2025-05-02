---
title: Unchanged
---
# Unchanged Datum Filter

The [Unchanged Datum Filter][src] provides a way to discard **entire datum** that have not changed
within a datum stream.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

!!! tip

	See the [Unchanged Property Filter](./unchanged-property.md) for a filter that can discard
	individual unchanging _properties_ within a datum stream.

## Settings

<figure markdown>
  ![Unchanged Datum filter component settings](../../images/users/datum-filters/unchanged-filter-settings@2x.png){width=697 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description |
|:-------------------|:------------|
| Unchanged Max Seconds | When greater than `0` then the maximum number of seconds to refrain from publishing an unchanged datum within a single datum stream. Use this setting to ensure a datum is included occasionally, even if the datum properties have not changed. Having at least one value per hour in a datum stream is recommended. This time period is always relative to the last unfiltered property within a given datum stream seen by the filter. |
| Property Pattern | A property name [pattern][regex] that limits the properties monitored for changes. Only property names that match this expression will be considered when determining if a datum differs from the previous datum within the datum stream. |

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-Unchanged.md
