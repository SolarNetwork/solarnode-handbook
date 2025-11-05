---
title: Split
---
# Split Datum Filter

The [Split Datum Filter][src] provides a way to split the properties of a [datum
stream](../datum.md) into multiple new derived datum streams.


This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Settings

<figure markdown>
  ![Split filter component settings](../../images/users/datum-filters/split-filter-settings@2x.png){width=1024 loading=lazy}
</figure>

In the example screen shot shown above, the `/power/meter/1` datum stream is split into two datum
streams: `/meter/1/power` and `/meter/1/energy`. Properties with names containing `current`,
`voltage`, or `power` (case-insensitive) will be copied to `/meter/1/power`. Properties with names
containing `hour` (case-insensitive) will be copied to `/meter/1/energy`.

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description |
|:-------------------|:------------|
| Swallow Input      | If enabled then discard input datum after splitting. Otherwise leave the input datum as is. |
| Persist Output     | If enabled then process and persist output datum after splitting. Otherwise process output datum but do not persist them. |
| Property Source Mappings |  A list of property name [regular expression][regex] with associated source IDs to copy matching properties to. |

## Property Source Mappings settings

Use the <kbd>+</kbd> and <kbd>-</kbd> buttons to add/remove Property Source Mapping configurations.

Each property source mapping configuration contains the following settings:

| Setting   | Description |
|:----------|:------------|
| Property  | A property name case-sensitive [regular expression][regex] to match on the input datum stream. You can enable case-insensitive matching by including a `(?i)` prefix. |
| Source ID | The destination source ID to copy the matching properties to. Supports [placeholders][placeholders]. |
| Rename    | An optional property name template to rename output properties with. Supports a `{p}` parameter for an input property name and pattern _capture groups_ from **Property** are available starting with `{1}`. For example a **Property** pattern `^(.*)_s1` with a **Rename** template `{1}` would map an input property `watts_s1` to `watts`. |

!!! tip

	If multiple property name expressions match the same property name, that property will
	be copied to **all** the datum streams of the associated source IDs.

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-Split.md
